# Go's `cobra` and `viper`

## Links

| Package | Link |
|:-------:|:-----|
|`cobra`    |https://github.com/spf13/cobra    |
|`cobra-cli`|https://github.com/spf13/cobra-cli|
|`viper`    |https://github.com/spf13/viper    |

## Marking flags

```go
func init() {
	// ...
	rootCmd.MarkFlagRequired("flag")
	rootCmd.MarkFlagDirname("flag")
	rootCmd.MarkFlagFilename("flag")
	rootCmd.MarkFlagsMutuallyExclusive("flag1", "flag1")
	rootCmd.MarkFlagsOneRequired("flag1", "flag2", "etc...")
	rootCmd.MarkFlagsRequiredTogether("flag1", "flag2", "etc...")
	// ...
}
```

## Custom flags validator

```go
var cmd = &cobra.Command{
  Short: "hello",
  Args: func(cmd *cobra.Command, args []string) error {
    if len(args) < 1 {
      return errors.New("requires at least one arg")
    }
    if myapp.IsValidColor(args[0]) {
      return nil
    }
    return fmt.Errorf("invalid color specified: %s", args[0])
  },
  Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("Hello, World!")
  },
}
```

## Limiting a flag to a set of options

On the root command, if flag is persistent across all commands:

```go
const (
	LogLevelInfo  = "info"
	LogLevelWarn  = "warn"
	LogLevelError = "error"
	LogLevelDebug = "debug"
)

var (
	ValidLogLevels = map[string]bool{
		LogLevelInfo:  true,
		LogLevelWarn:  true,
		LogLevelError: true,
		LogLevelDebug: true,
	}
)

func init() {
	// ...
	logLevelHelp := fmt.Sprintf(
		"log level: \"%s\", \"%s\", \"%s\", \"%s\"",
		cfg.LogLevelInfo,
		cfg.LogLevelWarn,
		cfg.LogLevelError,
		cfg.LogLevelDebug)
	rootCmd.PersistentFlags().StringVarP(&logLevel, cLogLevelFlag, "l", config.LogLevel, logLevelHelp)
	viper.BindPFlag(cNodeMode, nodeCmd.Flags().Lookup(cLogLevelFlag))
	err := nodeCmd.RegisterFlagCompletionFunc(cLogLevelFlag,
		func(cmd *cobra.Command, args []string, toComplete string) ([]string, cobra.ShellCompDirective) {
			return []string{cfg.LogLevelInfo, cfg.LogLevelWarn, cfg.LogLevelError, cfg.LogLevelDebug}, cobra.ShellCompDirectiveNoFileComp
		})
	if err != nil {
		log.Fatalf("Error registering flag completion function: %v", err)
		os.Exit(1)
	}
	// ...
}
```

On each command,  if flag is persistent across all commands:
```go
func runCommand(cmd *cobra.Command, args []string) {
	// ...
	if !cfg.ValidLogLevels[config.LogLevel] {
		log.Fatalf("wrong log level: \"%s\"", config.LogLevel)
		os.Exit(1)
	}
	// ...
}
```

## Single param

```go
	rootCmd = &cobra.Command{
		Version: "0.0.1",
		Use:     "go-downloader [flags] <url>",
		Args:    cobra.MinimumNArgs(1),
        // ...
    }
```

## Connecting with `viper`

```go
var (
	cfgFile string
	projectBase string
	userLicense string
)

func init() {
  cobra.OnInitialize(initConfig)
  rootCmd.PersistentFlags().StringVar(&cfgFile, "config", "", "config file (default is $HOME/.cobra.yaml)")
  rootCmd.PersistentFlags().StringVarP(&projectBase, "projectbase", "b", "", "base project directory eg. github.com/spf13/")
  rootCmd.PersistentFlags().StringP("author", "a", "YOUR NAME", "Author name for copyright attribution")
  rootCmd.PersistentFlags().StringVarP(
	&userLicense, 
	"license", 
	"l", 
	"", 
	"Name of license for the project (can provide `licensetext` in config)")
  rootCmd.PersistentFlags().Bool("viper", true, "Use Viper for configuration")
  viper.BindPFlag("author", rootCmd.PersistentFlags().Lookup("author"))
  viper.BindPFlag("projectbase", rootCmd.PersistentFlags().Lookup("projectbase"))
  viper.BindPFlag("useViper", rootCmd.PersistentFlags().Lookup("viper"))
  viper.SetDefault("author", "NAME HERE <EMAIL ADDRESS>")
  viper.SetDefault("license", "apache")
}

func initConfig() {
  // Don't forget to read config either from cfgFile or from home directory!
  if cfgFile != "" {
    // Use config file from the flag.
    viper.SetConfigFile(cfgFile)
  } else {
    // Find home directory.
    home, err := homedir.Dir()
    if err != nil {
      fmt.Println(err)
      os.Exit(1)
    }

    // Search config in home directory with name ".cobra" (without extension).
    viper.AddConfigPath(home)
    viper.SetConfigName(".cobra")
  }

  if err := viper.ReadInConfig(); err != nil {
    fmt.Println("Can't read config:", err)
    os.Exit(1)
  }
}
```

## Various fields

```go
	rootCmd = &cobra.Command{
		Version: "0.0.1",
		Use:     "go-downloader [flags] <url>",
		Args:    cobra.MinimumNArgs(1),
		Short:   "A multi-threads downloader",
		Long: `A downloader that uses multiple threads with overall progress.

It will default to download with 4 threads.
If the server does not support Range, or the size is unknown, it will do in a single thread.
It also keeps a state file in case of a possible resume.`,
		Example: `  # Downloads the file with all the defaults
  $ go-downloader "http://example.com/some_large_file.zip"

  # Attempts to resume a broken download
  $ go-downloader -r "http://example.com/some_large_file.zip"

  # Will give you debug information
  $ go-downloader -d "http://example.com/some_large_file.zip"`,
		Run: rootRun,
	}
```

## Setting the usage function

```go
func init() {
    // ...
	rootCmd.SetUsageFunc(rootUsageFunc)
}

func rootUsageFunc(cmd *cobra.Command) error {
	fmt.Print("\033[1mUSAGE\033[0m")
	if cmd.Runnable() {
		fmt.Printf("\n  %s", cmd.UseLine())
	}
	if cmd.HasAvailableSubCommands() {
		fmt.Printf("\n  %s [command]", cmd.CommandPath())
		if cmd.HasAvailableFlags() {
			fmt.Print(" [flags]")
		}
	}
	if len(cmd.Aliases) > 0 {
		fmt.Printf("\n\n\033[1mALIASES\033[0m\n")
		fmt.Printf("  %s", cmd.NameAndAliases())
	}
	if cmd.HasExample() {
		fmt.Printf("\n\n\033[1mEXAMPLES\033[0m\n")
		fmt.Printf("%s", cmd.Example)
	}
	if cmd.HasAvailableSubCommands() {
		cmds := cmd.Commands()
		if len(cmd.Groups()) == 0 {
			fmt.Printf("\n\n\033[1mAVAILABLE COMMANDS\033[0m")
			for _, subcmd := range cmds {
				if subcmd.IsAvailableCommand() || subcmd.Name() == "help" {
					fmt.Printf("\n  %s %s", rpad(subcmd.Name(), subcmd.NamePadding()), subcmd.Short)
				}
			}
		} else {
			for _, group := range cmd.Groups() {
				fmt.Printf("\n\n%s", group.Title)
				for _, subcmd := range cmds {
					if subcmd.GroupID == group.ID && (subcmd.IsAvailableCommand() || subcmd.Name() == "help") {
						fmt.Printf("\n  %s %s", rpad(subcmd.Name(), subcmd.NamePadding()), subcmd.Short)
					}
				}
			}
			if !cmd.AllChildCommandsHaveGroup() {
				fmt.Printf("\n\n\033[1mADDITIONAL COMMANDS\033[0m")
				for _, subcmd := range cmds {
					if subcmd.GroupID == "" && (subcmd.IsAvailableCommand() || subcmd.Name() == "help") {
						fmt.Printf("\n  %s %s", rpad(subcmd.Name(), subcmd.NamePadding()), subcmd.Short)
					}
				}
			}
		}
	}
	if cmd.HasAvailableLocalFlags() {
		fmt.Printf("\n\n\033[1mFLAGS\033[0m\n")
		fmt.Print(trimRightSpace(cmd.LocalFlags().FlagUsages()))
	}
	if cmd.HasAvailableInheritedFlags() {
		fmt.Printf("\n\n\033[1mGLOBAL FLAGS\033[0m\n")
		fmt.Print(trimRightSpace(cmd.InheritedFlags().FlagUsages()))
	}
	if cmd.HasHelpSubCommands() {
		fmt.Printf("\n\n\033[1mADDITIONAL HELP TOPICS\033[0m")
		for _, subcmd := range cmd.Commands() {
			if subcmd.IsAdditionalHelpTopicCommand() {
				fmt.Printf("\n  %s %s", rpad(subcmd.CommandPath(), subcmd.CommandPathPadding()), subcmd.Short)
			}
		}
	}

	fmt.Println("\n\n\033[1mPARAMS\033[0m")
	fmt.Print(lpad("url string", 2))
	fmt.Println(lpad("the URL to download (mandatory)", 12))

	if cmd.HasAvailableSubCommands() {
		fmt.Printf("\n\nUse \"%s [command] --help\" for more information about a command.", cmd.CommandPath())
	}
	fmt.Println()
	return nil
}

func trimRightSpace(s string) string {
	return strings.TrimRightFunc(s, unicode.IsSpace)
}

func rpad(s string, padding int) string {
	formattedString := fmt.Sprintf("%%-%ds", padding)
	return fmt.Sprintf(formattedString, s)
}

func lpad(s string, padding int) string {
	return strings.Repeat(" ", padding) + s
}
```

## Config example

Suggestion for alternative home dir: https://github.com/mitchellh/go-homedir

```go
package config

import (
	"os"
	"path"

	"github.com/spf13/cobra"
)

const (
	cConfigFolderName  = ".nosogod"
	cConfigFileName    = "config.toml"
	cLogsFolderName    = "logs"
	cLogLevel          = "info"
	cLogFileName       = "nosogod.log"
	cDatabasePath      = "data"
	DefaultNodeAddress = "0.0.0.0"
	DefaultNodePort    = 45050
	DefaultAPIAddress  = "127.0.0.1"
	DefaultAPIPort     = 45505
)

func homeFolder() string {
	home, err := os.UserHomeDir()
	cobra.CheckErr(err)

	return home
}

type Config struct {
	// Top level options use an anonymous struct
	BaseConfig `mapstructure:",squash"`
	API        *APIConfig  `mapstructure:"api"`
	Node       *NodeConfig `mapstructure:"node"`
}

// DefaultConfig Default configurable parameters.
func DefaultConfig() *Config {
	return &Config{
		BaseConfig: DefaultBaseConfig(),
		API:        DefaultAPIConfig(),
		Node:       DefaultNodeConfig(),
	}
}

func (c *Config) GetConfigFolder() string {
	if c.ConfigDir != "" {
		return c.ConfigDir
	} else {
		return path.Join(homeFolder(), cConfigFolderName)
	}
}

func (c *Config) GetConfigFile() string {
	if c.ConfigDir != "" {
		return path.Join(c.ConfigDir, cConfigFileName)
	} else {
		return path.Join(homeFolder(), cConfigFolderName, cConfigFileName)
	}
}

func (c *Config) GetLogsFolder() string {
	if c.ConfigDir != "" && c.LogFolder != "" {
		return path.Join(c.ConfigDir, c.LogFolder)
	} else {
		return path.Join(homeFolder(), cConfigFolderName, cLogsFolderName)
	}
}

func (c *Config) GetLogFile() string {
	if c.ConfigDir != "" && c.LogFolder != "" && c.LogFile != "" {
		return path.Join(c.ConfigDir, c.LogFolder, c.LogFile)
	} else {
		return path.Join(homeFolder(), cConfigFolderName, cLogsFolderName, cLogFileName)
	}
}

func (c *Config) GetDatabaseFolder() string {
	if c.ConfigDir != "" && c.DatabasePath != "" {
		return path.Join(c.ConfigDir, c.DatabasePath)
	} else {
		return path.Join(homeFolder(), cConfigFolderName, cDatabasePath)
	}
}

type BaseConfig struct {
	// The root directory for all data.
	// This should be set in viper so it can unmarshal into this struct
	ConfigDir string `mapstructure:"config_folder"`
	//log level to set
	LogLevel string `mapstructure:"log_level"`
	// log file name
	LogFolder string `mapstructure:"log_folder"`
	// log file name
	LogFile string `mapstructure:"log_file"`
	// LevelDB path
	DatabasePath string `mapstructure:"database_path"`
}

// DefaultBaseConfig Default configurable base parameters.
func DefaultBaseConfig() BaseConfig {
	return BaseConfig{
		LogLevel:     cLogLevel,
		LogFolder:    cLogsFolderName,
		LogFile:      cLogFileName,
		DatabasePath: cDatabasePath,
	}
}

type APIConfig struct {
	Address string `mapstructure:"address"`
	Port    int    `mapstructure:"port"`
}

func DefaultAPIConfig() *APIConfig {
	return &APIConfig{
		DefaultAPIAddress,
		DefaultAPIPort,
	}
}

type NodeConfig struct {
	Address string `mapstructure:"address"`
	Port    int    `mapstructure:"port"`
}

func DefaultNodeConfig() *NodeConfig {
	return &NodeConfig{
		DefaultNodeAddress,
		DefaultNodePort,
	}
}
```