# RunJAR - Portable JAR Runner

A lightweight script that downloads, caches, and runs Java applications without requiring system-level Java installation.

## Features

- **Zero Installation**: No need to install Java on your system
- **Smart Caching**: Downloads Java runtimes once, reuses them instantly
- **LTS-First Compatibility**: Tries multiple Java LTS versions automatically (24→21→17→11→8)
- **Dynamic Version Support**: Fetches available versions live from Adoptium OpenJDK
- **Clean Execution**: Temporary runtime, no system pollution

## Quick Start

```bash
# Run any JAR file (uses Java 24 LTS by default)
./runjar your-app.jar

# Get help
./runjar --help

# Show all supported versions (fetched live from Adoptium)
./runjar --list-versions

# Show cache information
./runjar --cache-info

# Use specific Java version
./runjar --java-version 11 your-app.jar

# Force workarounds for problematic JARs
./runjar --force-workarounds your-app.jar
```

## Technology Stack

- **Shell**: Pure Bash script for maximum compatibility
- **Java Source**: [Adoptium OpenJDK](https://adoptium.net/) (Eclipse Temurin distributions)
- **API**: Adoptium REST API for dynamic version discovery
- **Cache**: User-specific cache in `~/.cache/runjar/`
- **Runtime**: Temporary execution environment with automatic cleanup

## Automatic Fallback Strategy

**LTS-First Approach** (newest to oldest LTS):

1. **Primary**: User-specified version (default: Java 24)
2. **Fallback Order**: Java 21 → Java 17 → Java 11 → Java 8
3. **Skip Logic**: Automatically skips already-tried versions
4. **Workaround Attempts**: For each version, tries:
   - Standard execution
   - With `-Xverify:none` (bypasses bytecode verification)
   - With `-Djava.security.manager=` (relaxed security)
   - With both flags combined

## Why Avoid System Java?

- **Version Conflicts**: Different applications need different Java versions
- **Clean System**: No permanent installations cluttering your system
- **Security**: Isolated execution prevents system-wide Java vulnerabilities
- **Portability**: Works on any Linux system without prerequisites
- **Flexibility**: Easy to test applications with different Java versions
- **Modern Defaults**: Always uses latest LTS without system upgrades

## Cache System

**Smart Caching in `~/.cache/runjar/`:**

- **Size**: ~40MB per Java version (compressed)
- **Location**: User-specific cache directory
- **Reuse**: Downloaded once, used forever
- **Management**: Built-in cache info and cleanup commands
- **Offline**: Works without internet after first download

## Command Reference

```bash
# Version Management
./runjar --list-versions              # Show all supported versions
./runjar --java-version 17 app.jar   # Use specific version

# Cache Management
./runjar --cache-info                 # Show cache statistics
./runjar --clean-cache               # Clear all cached runtimes
./runjar --force-cleanup-runtimes    # Force cleanup all runjar temporary directories
./runjar --no-cache app.jar          # Force fresh download

# Troubleshooting
./runjar --force-workarounds app.jar # Skip standard execution, use workarounds
./runjar --no-fallback app.jar       # Disable automatic version fallback
./runjar --debug app.jar             # Show detailed download progress

# Runtime Management
./runjar --keep-runtime app.jar      # Don't delete runtime after execution
./runjar --dry-run app.jar           # Show what would be done
```

## Examples

```bash
# Modern application (uses Java 24 LTS)
./runjar spring-boot-app.jar

# Legacy application (force Java 8)
./runjar --java-version 8 old-app.jar

# Problematic JAR with workarounds
./runjar --force-workarounds problematic.jar

# Development with specific version
./runjar --java-version 21 --keep-runtime dev-app.jar

# Offline usage (uses cache)
./runjar --no-fallback cached-app.jar
```

## Installation

```bash
# Download the script
wget https://raw.githubusercontent.com/your-repo/runjar/main/runjar
chmod +x runjar

# Optional: Add to PATH
sudo cp runjar /usr/local/bin/
```

## Testing

### Local Testing with GitHub Actions

Run the exact same tests that run in CI using GitHub CLI's `act` extension:

```bash
# Install GitHub CLI and act extension if not already installed
# GitHub CLI: https://cli.github.com/
# Act extension: gh extension install https://github.com/nektos/gh-act

# Run all test jobs
gh act

# Run specific test job
gh act -j test-runjar

# Run with verbose output
gh act -v

# List available jobs
gh act -l
```

### GitHub Actions Workflow

The project includes comprehensive automated testing that covers:

- **All command-line flags and options** - Every feature flag is tested
- **Multiple Java versions** (8, 11, 17, 21, 24) - Version compatibility
- **Cache functionality** - Download, hit/miss, cleanup scenarios
- **Error handling** - Invalid JARs, missing files, bad options
- **Directory naming** - JAR filename inclusion in temp directories
- **Edge cases** - Special characters, long filenames, concurrent execution
- **Linux compatibility** - Comprehensive Linux testing
- **Performance testing** - Execution timing and resource usage

**Benefits of using `gh act` for local testing:**

- 🎯 **Exact CI environment** - Same containers as GitHub Actions
- 🚀 **Fast feedback** - Test locally before pushing
- 🔍 **Debug workflows** - Step through actions locally
- 💾 **Offline testing** - No need for internet after initial setup

## Development & Code Quality

### Pre-commit Hooks (Recommended)

This project uses [pre-commit](https://pre-commit.com/) for automated code quality checks. Contributors are
encouraged to set up pre-commit hooks to maintain consistent code quality and catch issues before they're committed.

## Contributions Welcome

Contributions, bug reports, and feature requests are welcome! Please feel free to:

- **Open an issue** for bugs or feature requests
- **Submit a pull request** with improvements
- **Test on Linux systems** and report compatibility
- **Improve documentation** or add examples

The project includes comprehensive test coverage - just run `gh act` to test your changes locally before submitting.

---

**RunJAR**: *Run Java applications anywhere, anytime, with the latest LTS versions - no installation required.*

**Powered by [Adoptium OpenJDK](https://adoptium.net/)**
