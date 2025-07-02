# Go code

## Hooking to signals

```go
func main(){
    // Create a cancellable context.
    ctx, cancel := context.WithCancel(context.Background())

    // Create a channel to receive OS signals.
    sigChan := make(chan os.Signal, 1)

    // Notify on all relevant Windows and Unix signals.
    signal.Notify(sigChan,
        // Windows signals
        os.Interrupt,    // Ctrl+C
        syscall.SIGTERM, // Termination signal
        syscall.SIGABRT, // Abort signal (Windows and Unix)

        // Unix/Linux signals
        syscall.SIGHUP,  // Hangup detected (terminal or process dies)
        syscall.SIGQUIT, // Quit from keyboard (Ctrl+\ on Unix)
        syscall.SIGINT,  // Interrupt from keyboard (Ctrl+C on Unix)
        // syscall.SIGTSTP, // Stop typed at terminal (Ctrl+Z on Unix)
        // syscall.SIGUSR1, // User-defined signal 1
        // syscall.SIGUSR2, // User-defined signal 2
    )
}

// On the blocking side
func (s *StructType) Run() {
	ticker := time.NewTicker(time.Second * 5)
	defer ticker.Stop()

    for {
		select {
		case <-n.ctx.Done():
			log.Debug("Node.Start() exiting")
			return
        }
		case <-ticker.C:
          // Do something every 5 seconds
 		default:
			continue
   }
}
```

## Logging

```go
package logger

import (
	"io"
	"os"
	"time"

	"github.com/rs/zerolog"
	"github.com/spf13/cobra"
)

var (
	logger zerolog.Logger
)

func init() {
	logger = zerolog.New(zerolog.ConsoleWriter{
		Out:        os.Stderr,
		TimeFormat: time.DateTime,
	}).
		With().
		Timestamp().
		Logger()
}

func SetFileAndLevel(logFile string, logLevel string) {
	switch logLevel {
	case "info":
		zerolog.SetGlobalLevel(zerolog.InfoLevel)
	case "warn":
		zerolog.SetGlobalLevel(zerolog.WarnLevel)
	case "error":
		zerolog.SetGlobalLevel(zerolog.ErrorLevel)
	case "debug":
		zerolog.SetGlobalLevel(zerolog.DebugLevel)
	default:
		zerolog.SetGlobalLevel(zerolog.InfoLevel)
	}

	// Initialize the logger
	if logFile == "" {
		logger = zerolog.New(zerolog.ConsoleWriter{
			Out:        os.Stderr,
			TimeFormat: time.DateTime,
		}).
			With().
			Timestamp().
			Logger()
	} else {
		file, err := os.OpenFile(
			logFile,
			os.O_CREATE|os.O_APPEND|os.O_WRONLY,
			0644,
		)
		cobra.CheckErr(err)

		var console io.Writer = zerolog.ConsoleWriter{
			Out:        os.Stderr,
			TimeFormat: time.DateTime,
		}
		output := zerolog.MultiLevelWriter(console, file)

		logger = zerolog.New(output).
			With().
			Timestamp().
			Logger()

		Infof("Logging to: '%s', with log level '%s'", logFile, logLevel)
	}
}

func Print(msg string) {
	logger.Print(msg)
}

func Printf(msg string, v ...interface{}) {
	logger.Printf(msg, v...)
}

func Println(msg string) {
	logger.Println(msg)
}

func Info(msg string) {
	logger.Info().Msg(msg)
}

func Infof(format string, v ...interface{}) {
	logger.Info().Msgf(format, v...)
}

func Warn(msg string) {
	logger.Warn().Msg(msg)
}

func Warnf(format string, v ...interface{}) {
	logger.Warn().Msgf(format, v...)
}

func Error(msg string, err error) {
	if err == nil {
		logger.Error().Msg(msg)
	} else {
		logger.Error().Err(err).Msg(msg)
	}
}

func Errorf(format string, err error, v ...interface{}) {
	if err == nil {
		logger.Error().Msgf(format, v...)
	} else {
		logger.Error().Err(err).Msgf(format, v...)
	}
}

func Fatal(msg string) {
	logger.Fatal().Msg(msg)
}

func Fatalf(format string, v ...interface{}) {
	logger.Fatal().Msgf(format, v...)
}

func Debug(msg string) {
	logger.Debug().Msg(msg)
}

func Debugf(format string, v ...interface{}) {
	logger.Debug().Msgf(format, v...)
}
```

## Version

```go
package version

import "fmt"

var (
	VersionMajor = 0
	VersionMinor = 0
	VersionPatch = 2
	Version      = "v0.0.2"
	Name         = "nosogo"
	// GitCommit is set with --ldflags "-X main.gitCommit=$(git rev-parse HEAD)"
	GitCommit string
	Title     = fmt.Sprintf("%s %s", Name, Version)
)

func init() {
	if GitCommit != "" {
		Version += "+" + GitCommit[:8]
	}
}
```

## Example Makefile with version above

```makefile
ifndef GOOS
UNAME_S := $(shell uname -s)
ifeq ($(UNAME_S),Darwin)
	GOOS := darwin
else ifeq ($(UNAME_S),Linux)
	GOOS := linux
else
#$(error "$$GOOS is not defined. If you are using Windows, try to re-make using 'GOOS=windows make ...' ")
	GOOS := windows
endif
endif

BUILD_FLAGS := -ldflags "-X nosogod/version.GitCommit=`git rev-parse HEAD`"

NOSOGOD_BINARY64 := nosogod-$(GOOS)_amd64
NOSOGOCLI_BINARY64 := nosogocli-$(GOOS)_amd64

VERSION := $(shell grep -E "Version[ ]*=" version/version.go | tr -d "\" " | cut -d "=" -f 2)

NOSOGOD_RELEASE64 := nosogod-$(VERSION)-$(GOOS)_amd64
NOSOGOCLI_RELEASE64 := nosogocli-$(VERSION)-$(GOOS)_amd64

NOSOGO_RELEASE64 := nosogo-$(VERSION)-$(GOOS)_amd64

all: test target release-all install
#all: target release-all install

nosogod:
	@echo "Building nosogod to bin/nosogod"
	@mkdir -p bin
	@go build $(BUILD_FLAGS) -o bin/nosogod cmd/nosogod/main.go

nosogocli:
	@echo "Building nosogocli to bin/nosogocli"
	@mkdir -p bin
	@go build $(BUILD_FLAGS) -o bin/nosogocli cmd/nosogocli/main.go

install:
	@echo "Installing nossogod to $(GOPATH)/bin"
	@go install ./bin/nosogod
	@go install ./bin/nosogocli

target:
	mkdir -p $@

binary: target/$(NOSOGOD_BINARY64) target/$(NOSOGOCLI_BINARY64)

ifeq ($(GOOS),windows)
release: binary
	cd target && cp -f $(NOSOGOD_BINARY64) $(NOSOGOD_BINARY64).exe
	cd target && cp -f $(NOSOGOCLI_BINARY64) $(NOSOGOCLI_BINARY64).exe
	cd target && md5sum $(NOSOGOD_BINARY64).exe $(NOSOGOCLI_BINARY64).exe  >$(NOSOGO_RELEASE64).md5
	cd target && zip $(NOSOGO_RELEASE64).zip $(NOSOGOD_BINARY64).exe $(NOSOGOCLI_BINARY64).exe $(NOSOGO_RELEASE64).md5
	cd target && rm -f $(NOSOGOD_BINARY64) $(NOSOGOD_BINARY64).exe $(NOSOGOCLI_BINARY64) $(NOSOGOCLI_BINARY64).exe $(NOSOGO_RELEASE64).md5
else
release: binary
	cd target && md5sum $(NOSOGOD_BINARY64) $(NOSOGOCLI_BINARY64) >$(NOSOGO_RELEASE64).md5
	cd target && tar -czf $(NOSOGO_RELEASE64).tgz $(NOSOGOD_BINARY64) $(NOSOGOCLI_BINARY64) $(NOSOGO_RELEASE64).md5
	cd target && rm -f $(NOSOGOD_BINARY64) $(NOSOGOCLI_BINARY64) $(NOSOGO_RELEASE64).md5
endif

release-all: clean
	GOOS=darwin  make release
	GOOS=linux   make release
	GOOS=windows make release

clean:
	@echo "Cleaning binaries built..."
	@rm -f bin/nosogod bin/nosogocli
	@rm -rf target
	@rm -f $(GOPATH)/bin/nosogod
	@rm -f $(GOPATH)/bin/nosogocli
	@echo "Done."

target/$(NOSOGOD_BINARY64):
	CGO_ENABLED=0 GOARCH=amd64 go build $(BUILD_FLAGS) -o $@ cmd/nosogod/main.go

target/$(NOSOGOCLI_BINARY64):
	CGO_ENABLED=0 GOARCH=amd64 go build $(BUILD_FLAGS) -o $@ cmd/nosogocli/main.go

test:
	@echo "====> Running go test"
	@go test ./tests

.PHONY: all target release-all clean
```