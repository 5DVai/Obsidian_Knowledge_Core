# Terminal Handler for WebSocket-based Remote Shell Access
**Source:** command-line-tools\agent\internal\terminal_handler.go  
**Ingestion Date:** 2025-11-28

## Executive Summary
The `terminal_handler.go` file is a critical component of a command-line tool designed to facilitate remote shell access via WebSockets. This file implements a WebSocket server that upgrades HTTP connections to WebSocket connections, allowing for real-time, bidirectional communication between a client and a server-hosted terminal session. The primary function of this implementation is to manage terminal sessions, handle input/output streams, and ensure secure and efficient communication over the WebSocket protocol. This functionality is particularly valuable in enterprise environments where remote management and automation of command-line tasks are required.

The code leverages several libraries and patterns to achieve its goals, including the use of the `pty` package for pseudo-terminal management and the `gorilla/websocket` package for WebSocket communication. It also incorporates robust error handling and logging mechanisms using the `zap` logging library, ensuring that any issues during the terminal session are promptly reported and managed. This file exemplifies a reusable pattern for integrating WebSocket-based terminal access into enterprise-grade applications.

## Key Concepts & Principles
- **WebSocket Communication:** Utilizes the `gorilla/websocket` package to establish and manage WebSocket connections for real-time communication.
- **Pseudo-terminal Management:** Employs the `pty` package to create and manage pseudo-terminal sessions, enabling remote shell access.
- **Error Handling and Logging:** Implements comprehensive error handling and logging using the `zap` library to ensure reliability and traceability.
- **Configuration Management:** Reads configuration settings from a JSON file to customize the behavior of the terminal sessions.
- **Security Considerations:** Includes token-based request validation to ensure that only authorized users can initiate terminal sessions.

## Detailed Technical Analysis

### WebSocket Server Setup
The file defines a WebSocket server using the `gorilla/websocket` package. The server upgrades incoming HTTP requests to WebSocket connections, allowing for full-duplex communication. The `upgrader` object is configured with buffer sizes to optimize performance.

### Terminal Session Management
The core functionality revolves around managing terminal sessions using the `pty` package. The `exec.Command` function is used to start a shell session, and the `pty.Start` function attaches the session to a pseudo-terminal. This setup allows for capturing and redirecting input/output streams between the client and the server.

### Error Handling and Logging
The implementation includes extensive error handling and logging using the `zap` library. Errors during WebSocket communication, terminal session management, and configuration loading are logged with detailed messages, aiding in troubleshooting and maintenance.

### Configuration and Security
Configuration settings are loaded from a JSON file, allowing for flexible customization of the terminal handler's behavior. Security is addressed through token-based validation, ensuring that only authenticated requests can initiate terminal sessions.

## Enterprise Q&A Bank

1. **Q:** How does the terminal handler ensure secure communication?
   **A:** It uses WebSocket protocol with token-based request validation to authenticate users before establishing a terminal session.

2. **Q:** What libraries are used for WebSocket and terminal management?
   **A:** The `gorilla/websocket` package is used for WebSocket communication, and the `pty` package is used for managing pseudo-terminal sessions.

3. **Q:** How are errors handled in the terminal handler?
   **A:** Errors are logged using the `zap` logging library, providing detailed information for troubleshooting.

4. **Q:** Can the terminal handler be configured for different environments?
   **A:** Yes, configuration settings are loaded from a JSON file, allowing for environment-specific customization.

5. **Q:** What happens if the terminal session encounters an error?
   **A:** The error is logged, and the session is gracefully terminated to prevent resource leaks.

## Actionable Takeaways
- Implement WebSocket-based communication for real-time, bidirectional data exchange in remote management applications.
- Use the `pty` package to manage pseudo-terminal sessions for remote shell access.
- Incorporate robust error handling and logging to ensure reliability and maintainability.
- Secure WebSocket connections with token-based authentication to prevent unauthorized access.
- Load configuration settings from external files to enable flexible deployment across different environments.

---
**Raw Content:**
```go
package internal

import (
	"encoding/json"
	"errors"
	"fmt"
	"io"
	"net/http"
	"os"
	"os/exec"
	"reconmap/agent/internal/configuration"
	"syscall"
	"unsafe"

	"github.com/creack/pty"
	"github.com/gorilla/websocket"
	sharedconfig "github.com/reconmap/shared-lib/pkg/configuration"
	"go.uber.org/zap"
)

const (
	bufferSizeBytes = 1024
)

var upgrader = websocket.Upgrader{
	ReadBufferSize:  bufferSizeBytes,
	WriteBufferSize: bufferSizeBytes,
}

type windowSize struct {
	Rows uint16 `json:"rows"`
	Cols uint16 `json:"cols"`
	X    uint16
	Y    uint16
}

func tryWriteMessage(conn *websocket.Conn, messageType int, data []byte) {
	if err := conn.WriteMessage(messageType, data); err != nil {
		logger.Error("Unable to write message", zap.Error(err))
	}
}

func tryWriteTextMessage(conn *websocket.Conn, str string) {
	tryWriteMessage(conn, websocket.TextMessage, []byte(str))
}

func tryWriteBinaryMessage(conn *websocket.Conn, data []byte) {
	tryWriteMessage(conn, websocket.BinaryMessage, data)
}

func handleWebsocket(w http.ResponseWriter, r *http.Request) {
	config, _ := sharedconfig.ReadConfig[configuration.Config]("config-reconmapd.json")

	err := CheckRequestToken(r)
	if err != nil {
		logger.Error(err)
		return
	}

	conn, err := UpgradeRequest(w, r)
	if err != nil {
		logger.Error(err)
		return
	}

	l := logger.With("remoteaddr", r.RemoteAddr)

	params := r.URL.Query()

	cmd := exec.Command("/bin/bash", "-l")
	cmd.Dir = config.AgentDirectory
	cmd.Env = append(os.Environ(),
		"LANG=en_GB.UTF-8",
		"TERM=xterm-256colors",
		"RMAP_SESSION_TOKEN="+params.Get("token"))

	ttyFile, err := pty.Start(cmd)
	if err != nil {
		l.Error("Unable to start pty/cmd", zap.Error(err))
		tryWriteTextMessage(conn, err.Error())
		return
	}
	defer func() {
		var err error
		if err = cmd.Process.Kill(); err != nil {
			l.Error("Couldn't kill process", zap.Error(err))
		}
		if _, err = cmd.Process.Wait(); err != nil {
			l.Error("Couldn't wait for process", zap.Error(err))
		}
		if err = ttyFile.Close(); err != nil {
			l.Error("Couldn't close tty", zap.Error(err))
		}
		if err = conn.Close(); err != nil {
			l.Error("Couldn't close connection", zap.Error(err))
		}
	}()

	go sendTtyBuffer(l, ttyFile, conn)

	for {
		err := receiveWsBuffer(l, conn, ttyFile)
		if err != nil {
			l.Error("unable to read from conn (%w)", err)
			break
		}
	}
}

func sendTtyBuffer(l *zap.SugaredLogger, ttyFile *os.File, conn *websocket.Conn) {
	for {
		buf := make([]byte, bufferSizeBytes)
		read, err := ttyFile.Read(buf)
		if err != nil {
			l.Errorf("unable to read from tty (%w)", err)
			return
		}
		tryWriteBinaryMessage(conn, buf[:read])
	}
}

func receiveWsBuffer(l *zap.SugaredLogger, conn *websocket.Conn, ttyFile *os.File) error {
	messageType, reader, err := conn.NextReader()
	if err != nil {
		if websocket.IsCloseError(err,
			websocket.CloseNormalClosure,   // Normal.
			websocket.CloseAbnormalClosure, // OpenSSH killed proxy client.
		) {
			return fmt.Errorf("received closed error (%w)", err)
		}

		return fmt.Errorf("Unable to grab next reader (%w)", err)
	}

	if messageType == websocket.CloseMessage {
		return errors.New("close message received")
	}

	if messageType == websocket.TextMessage {
		tryWriteTextMessage(conn, "Unexpected text message")
		return errors.New("unexpected text message")
	}

	dataTypeBuf := make([]byte, 1)
	read, err := reader.Read(dataTypeBuf)
	if err != nil {
		tryWriteTextMessage(conn, "Unable to read message type from reader")
		return fmt.Errorf("unable to read message type from reader (%w)", err)
	}

	if read != 1 {
		return fmt.Errorf("unexpected number of bytes read (%d)", read)
	}

	switch dataTypeBuf[0] {
	case 0:
		bytesWritten, err := io.Copy(ttyFile, reader)
		if err != nil {
			l.Errorf("Error after copying %d bytes", bytesWritten)
		}
	case 1:
		winSize, err := tryDecodeWindowSize(reader)
		if err != nil {
			tryWriteTextMessage(conn, "Error decoding resize message: "+err.Error())
			return err
		}
		resizeTerminal(l, winSize, ttyFile)
	default:
		l.Error("Unknown data type", zap.Uint8("dataType", dataTypeBuf[0]))
	}

	return nil
}

func tryDecodeWindowSize(reader io.Reader) (windowSize, error) {
	winSize := windowSize{}
	decoder := json.NewDecoder(reader)
	err := decoder.Decode(&winSize)
	return winSize, err
}

// #nosec G103
// getString converts byte slice to a string without memory allocation.
// See https://groups.google.com/forum/#!msg/Golang-Nuts/ENgbUzYvCuU/90yGx7GUAgAJ
func resizeTerminal(l *zap.SugaredLogger, winSize windowSize, ttyFile *os.File) {
	l.Info("Resizing terminal", zap.Uint16("rows", winSize.Rows), zap.Uint16("cols", winSize.Cols))
	_, _, errno := syscall.Syscall(
		syscall.SYS_IOCTL,
		ttyFile.Fd(),
		syscall.TIOCSWINSZ,
		uintptr(unsafe.Pointer(&winSize)),
	)
	if errno != 0 {
		l.Error("unable to resize terminal", zap.Error(errno))
	}
}
```