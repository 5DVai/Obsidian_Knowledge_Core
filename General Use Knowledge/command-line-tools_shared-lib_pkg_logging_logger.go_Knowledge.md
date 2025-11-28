# Enterprise-Grade Logging Utility with Singleton Pattern
**Source:** command-line-tools\shared-lib\pkg\logging\logger.go  
**Ingestion Date:** 2025-11-28

## Executive Summary
This file implements a logging utility using the `zap` logging library, which is known for its high performance and structured logging capabilities. The utility is designed to provide a singleton instance of a logger, ensuring that only one instance is used throughout the application. This is achieved using the `sync.Once` construct, which guarantees that the logger is initialized only once, even in concurrent environments. The logger is configured to output logs both to a file and the console, with a custom time encoder for log entries.

The utility is valuable for enterprise applications that require efficient and consistent logging mechanisms. By encapsulating the logger initialization and configuration, it promotes code reuse and reduces the likelihood of errors related to logger misconfiguration. The use of the `zap` library further enhances the utility's performance, making it suitable for high-throughput applications.

## Key Concepts & Principles
- **Singleton Pattern:** Ensures a single instance of the logger is used across the application.
- **Structured Logging:** Utilizes `zap` for efficient, structured log output.
- **Concurrency Safety:** Uses `sync.Once` to safely initialize the logger in concurrent environments.
- **Custom Time Encoding:** Implements a custom time format for log entries.

## Detailed Technical Analysis

### Singleton Logger Initialization
The `GetLoggerInstance` function employs the singleton pattern using `sync.Once` to ensure that the logger is initialized only once. This pattern is crucial in preventing multiple logger instances, which could lead to inconsistent logging behavior.

### Logger Configuration
The logger is configured with two encoders: a JSON encoder for file output and a console encoder for terminal output. The `zapcore.NewTee` function is used to direct log entries to both destinations simultaneously. This dual-output configuration is beneficial for applications that require persistent log storage and real-time monitoring.

### Custom Time Encoding
The `OnlyTimeEncoder` function customizes the time format in log entries to display only the time of day. This can be particularly useful in scenarios where date information is redundant or where logs are rotated daily.

## Enterprise Q&A Bank

1. **Q:** Why use the singleton pattern for the logger?
   **A:** To ensure that all parts of the application use the same logger instance, preventing configuration discrepancies and reducing resource usage.

2. **Q:** What are the benefits of using `zap` for logging?
   **A:** `zap` provides high-performance, structured logging, which is crucial for applications that require efficient log processing and analysis.

3. **Q:** How does `sync.Once` contribute to concurrency safety?
   **A:** `sync.Once` ensures that the logger initialization code is executed only once, even when accessed by multiple goroutines, preventing race conditions.

4. **Q:** What is the purpose of custom time encoding in logs?
   **A:** Custom time encoding allows for tailored log formats, which can improve readability and relevance depending on the application's logging requirements.

5. **Q:** How does the logger handle log file creation and writing?
   **A:** The logger opens a file in append mode, creating it if it doesn't exist, and writes log entries using a JSON encoder for structured storage.

## Actionable Takeaways
- Implement the singleton pattern for shared resources like loggers to ensure consistent usage and configuration.
- Use structured logging libraries like `zap` for high-performance applications.
- Customize log formats to suit application-specific needs, enhancing log readability and utility.
- Ensure concurrency safety in logger initialization to prevent race conditions.
- Configure loggers to output to multiple destinations for comprehensive logging solutions.

---
**Raw Content:**
```go
package logging

import (
	"os"
	"sync"
	"time"

	"go.uber.org/zap"
	"go.uber.org/zap/zapcore"
)

var logger *zap.SugaredLogger
var once sync.Once

func GetLoggerInstance() *zap.SugaredLogger {
	once.Do(func() {
		logger = initLogger()
	})
	return logger
}

func OnlyTimeEncoder(t time.Time, enc zapcore.PrimitiveArrayEncoder) {
	enc.AppendString(t.Format("15:04:05"))
}

func initLogger() *zap.SugaredLogger {

	encoderConfig := zap.NewProductionEncoderConfig()

	fileEncoder := zapcore.NewJSONEncoder(encoderConfig)

	encoderConfig.EncodeTime = OnlyTimeEncoder
	consoleEncoder := zapcore.NewConsoleEncoder(encoderConfig)

	fd, err := os.OpenFile("rmap.log", os.O_WRONLY|os.O_APPEND|os.O_CREATE, os.FileMode(0644))
	if err != nil {
		panic(err)
	}

	level := zap.InfoLevel

	core := zapcore.NewTee(
		zapcore.NewCore(fileEncoder, zapcore.AddSync(fd), level),
		zapcore.NewCore(consoleEncoder, zapcore.AddSync(os.Stdout), level),
	)

	logger := zap.New(core)

	return logger.Sugar()
}
```