# Reconmap Agent Application Architecture
**Source:** command-line-tools\agent\internal\app.go  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `app.go` file is a critical component of the Reconmap agent, responsible for initializing and running the agent's core functionalities. It sets up the HTTP server, manages Redis connections, and schedules command executions using cron jobs. The file demonstrates a robust architectural pattern for building a command-line tool that integrates with external APIs and services. It leverages Go's concurrency features to handle multiple tasks efficiently, making it a valuable resource for designing scalable and maintainable software systems.

## Key Concepts & Principles
- **Dependency Injection:** The `App` struct encapsulates dependencies like Redis and HTTP router, promoting modularity.
- **Concurrency:** Utilizes Go routines and wait groups to manage asynchronous tasks.
- **Configuration Management:** Reads configuration from JSON files, allowing for flexible deployment.
- **Cron Scheduling:** Uses cron jobs to schedule and execute commands at specified intervals.
- **Logging:** Implements structured logging with Zap for better traceability and debugging.

## Detailed Technical Analysis

### Architectural Patterns
The file employs a service-oriented architecture where the `App` struct acts as a service container. This pattern is beneficial for managing dependencies and lifecycle events in a clean and organized manner.

### Concurrency and Scheduling
The use of Go routines and the `sync.WaitGroup` allows the application to perform non-blocking operations, such as executing commands and capturing their output. The cron library is used to schedule tasks, demonstrating an effective way to handle periodic operations in a Go application.

### Configuration and Environment Management
Configuration is read from a JSON file using the `sharedconfig.ReadConfig` function, which abstracts the configuration management and allows for easy changes without modifying the codebase. Environment variables are set dynamically for command execution, showcasing a flexible approach to environment management.

### Error Handling and Logging
The application uses structured logging with the Zap library, providing detailed logs that are crucial for debugging and monitoring. Error handling is done gracefully, with informative messages logged for troubleshooting.

## Enterprise Q&A Bank

1. **Q:** How does the application manage its dependencies?
   **A:** Dependencies are encapsulated within the `App` struct, promoting modularity and ease of testing.

2. **Q:** What concurrency model does the application use?
   **A:** The application uses Go routines and wait groups to handle concurrent tasks efficiently.

3. **Q:** How are scheduled tasks managed in the application?
   **A:** Scheduled tasks are managed using the cron library, which allows for executing commands at specified intervals.

4. **Q:** How does the application handle configuration?
   **A:** Configuration is managed through JSON files, which are read at runtime, allowing for flexible deployment configurations.

5. **Q:** What logging strategy is employed by the application?
   **A:** The application uses structured logging with the Zap library, providing detailed and structured log outputs.

6. **Q:** How does the application ensure secure command execution?
   **A:** Commands are executed with environment variables set dynamically, and outputs are captured and logged for auditing.

7. **Q:** What is the role of Redis in the application?
   **A:** Redis is used for managing state and communication between different components of the application.

8. **Q:** How does the application handle errors during command execution?
   **A:** Errors are logged with detailed messages, and the application attempts to recover gracefully where possible.

9. **Q:** How is the application's HTTP server initialized?
   **A:** The HTTP server is initialized using the Gorilla Mux router, which handles incoming requests and routes them to appropriate handlers.

10. **Q:** What measures are in place for system monitoring?
    **A:** The application periodically pings the Reconmap API and logs system information, aiding in monitoring and diagnostics.

## Actionable Takeaways
- Use structured logging to enhance traceability and debugging.
- Encapsulate dependencies within a struct to promote modularity.
- Leverage Go's concurrency features for efficient task management.
- Implement cron jobs for scheduling periodic tasks.
- Manage configuration through external files for flexibility.
- Ensure secure command execution by managing environment variables dynamically.
- Use Redis for state management and inter-component communication.

---
**Raw Content:**
```go
package internal

import (
	"net/http"
	"os"
	"os/exec"
	"reconmap/agent/internal/build"
	"reconmap/agent/internal/configuration"
	"runtime"
	"strings"
	"sync"
	"time"

	"fmt"
	"log"
	"net"

	"github.com/go-redis/redis/v8"
	"github.com/gorilla/mux"
	"github.com/reconmap/shared-lib/pkg/api"
	sharedconfig "github.com/reconmap/shared-lib/pkg/configuration"
	sharedio "github.com/reconmap/shared-lib/pkg/io"
	"github.com/reconmap/shared-lib/pkg/logging"
	"github.com/robfig/cron"
	"github.com/shirou/gopsutil/v4/cpu"
	"github.com/shirou/gopsutil/v4/mem"
	"go.uber.org/zap"
)

// App contains properties needed for agent
// to connect to redis and http router.
type App struct {
	redisConn *redis.Client
	muxRouter *mux.Router
	Logger    *zap.SugaredLogger
}

var logger = logging.GetLoggerInstance()

// NewApp returns a App struct that has intialized a redis client and http router.
func NewApp() App {
	muxRouter := mux.NewRouter()
	muxRouter.HandleFunc("/term", handleWebsocket)

	return App{
		muxRouter: muxRouter,
		Logger:    logging.GetLoggerInstance(),
	}
}

func GetLocalIP() net.IP {
	conn, err := net.Dial("udp", "8.8.8.8:80")
	if err != nil {
		log.Fatal(err)
	}
	defer conn.Close()

	localAddress := conn.LocalAddr().(*net.UDPAddr)

	return localAddress.IP
}

// Run starts the agent.
func (app *App) Run(listenAddress string) error {
	app.Logger.Info("Reconmap agent starting...")

	config, err := sharedconfig.ReadConfig[configuration.Config]("config-reconmapd.json")
	if err != nil {
		app.Logger.Error("unable to read reconmapd config", zap.Error(err))
		return err
	}

	accessToken, err := GetAccessToken(app)
	if err != nil {
		return fmt.Errorf("unable to login to keycloak (%w)", err)
	}

	restApiUrl := config.ReconmapApiConfig.BaseUri
	schedules, err := api.GetCommandsSchedules(restApiUrl, accessToken)
	if err != nil {
		app.Logger.Error("unable to get command schedules", zap.Error(err))
	}

	app.Logger.Info("creating cron jobs")
	c := cron.New()

	for _, commandSchedule := range *schedules {
		c.AddFunc(commandSchedule.CronExpression, func() {
			parts := strings.Split(commandSchedule.ArgumentValues, " ")
			cmd := exec.Command(parts[0], parts[1:]...) // #nosec G204
			cmd.Env = append(os.Environ(), "PS1=# ")
			cmd.Env = append(cmd.Env, "TERM=xterm")
			cmd.Env = append(cmd.Env, "RMAP_SESSION_TOKEN="+accessToken)
			var stdout, stderr []byte
			var errStdout, errStderr error
			stdoutIn, _ := cmd.StdoutPipe()
			stderrIn, _ := cmd.StderrPipe()
			err := cmd.Start()
			if err != nil {
				app.Logger.Fatalf("cmd.Start() failed with '%s'\n", err)
			}
			var wg sync.WaitGroup
			wg.Add(1)
			go func() {
				stdout, errStdout = sharedio.CopyAndCapture(os.Stdout, stdoutIn)
				wg.Done()
			}()

			stderr, errStderr = sharedio.CopyAndCapture(os.Stderr, stderrIn)

			wg.Wait()

			err = cmd.Wait()
			if err != nil {
				if errStderr != nil {
					print(errStderr)
				}
				app.Logger.Fatalf("cmd.Run() failed with %s\n", err)
			}
			if errStdout != nil || errStderr != nil {
				app.Logger.Fatal("failed to capture stdout or stderr\n")
			}
			outStr, errStr := string(stdout), string(stderr)
			app.Logger.Debug(outStr)
			app.Logger.Debug(errStr)
		})
	}
	c.Start()

	redisErr := app.connectRedis()
	if redisErr != nil {
		errorFormatted := fmt.Errorf("unable to connect to redis (%w)", *redisErr)
		return errorFormatted
	}

	ticker := time.NewTicker(1 * time.Minute)
	defer ticker.Stop()

	go func() {

		hostname, err := os.Hostname()
		if err != nil {
			hostname = "unknown"
		}
		cpuInfo, err := cpu.Counts(true)
		cpuStr := "unknown"

		if err == nil {
			cpuStr = fmt.Sprintf("%d cores", cpuInfo)
		}

		vmStat, err := mem.VirtualMemory()
		memStr := "unknown"
		if err == nil {
			memStr = fmt.Sprintf("%.2fGB", float64(vmStat.Total)/(1024*1024*1024))
		}

		systemInfo := api.SystemInfo{
			Version:       build.BuildVersion,
			Hostname:      hostname,
			Arch:          runtime.GOARCH,
			Os:            runtime.GOOS,
			CPU:           cpuStr,
			Memory:        memStr,
			IpAddress:     GetLocalIP().String(),
			ListenAddress: listenAddress,
		}

		api.AgentBoot(restApiUrl, accessToken, &systemInfo)
	}()
	go func() {
		for range ticker.C {
			app.Logger.Info("pinging reconmap API")
			api.AgentPing(restApiUrl, accessToken)
		}
	}()

	go broadcastNotifications(app)

	if err := http.ListenAndServe(listenAddress, app.muxRouter); err != nil {
		app.Logger.Fatal("Something went wrong with the webserver", zap.Error(err))
	}

	return nil
}
```