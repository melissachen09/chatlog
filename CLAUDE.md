# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Chatlog is a Go-based WeChat chat history extraction and analysis tool that supports Windows and macOS platforms. It provides Terminal UI, HTTP API, and MCP (Model Context Protocol) integration for AI assistants.

## Build and Development Commands

### Primary Commands (via Makefile)
```bash
# Full build pipeline (clean, lint, tidy, test, build)
make all

# Individual commands
make clean     # Remove bin/ directory
make lint      # Run golangci-lint
make tidy      # Run go mod tidy
make test      # Run tests with coverage
make build     # Build for current platform
make crossbuild # Build for multiple platforms
```

### Direct Go Commands
```bash
# Run the application
go run main.go

# Build manually
CGO_ENABLED=1 go build -o bin/chatlog main.go

# Run tests
go test ./... -cover

# Install dependencies
go mod tidy
```

## Architecture Overview

### Core Components

1. **Terminal UI Application** (`internal/chatlog/app.go`)
   - Uses tview library for Terminal UI
   - Manages multiple pages: menu, help, forms
   - Handles user interactions and navigation

2. **WeChat Integration** (`internal/wechat/`)
   - **Account Management**: Multi-account support with status tracking
   - **Key Extraction**: Platform-specific key extraction (Darwin/Windows, v3/v4)
   - **Decryption**: Database decryption with validation
   - **Process Detection**: Auto-detection of WeChat processes

3. **Database Layer** (`internal/wechatdb/`)
   - SQLite database handling with multiple datasources
   - Repository pattern for data access
   - Support for different WeChat versions (v3, v4, Darwin variants)

4. **HTTP API Server** (`internal/chatlog/http/`)
   - RESTful API for chat history access
   - Serves static files and media content
   - Real-time decryption of encrypted images

5. **MCP Integration** (`internal/mcp/`)
   - Server-Sent Events (SSE) endpoint for AI assistant integration
   - Session management for concurrent connections
   - JSON-RPC protocol implementation

### Key Directories

- `cmd/chatlog/`: CLI command implementations (decrypt, key, server, etc.)
- `internal/model/`: Data models for different WeChat versions
- `internal/errors/`: Custom error types and HTTP error handling
- `internal/ui/`: Terminal UI components (menu, forms, help)
- `pkg/`: Utility packages (config, version, file operations)

## Platform-Specific Features

### macOS Support
- SIP (System Integrity Protection) handling required for key extraction
- Memory inspection via vmmap and process tools
- Darwin-specific v3/v4 implementations

### Windows Support
- Process memory access for key extraction
- Windows-specific database paths and structures
- Cross-architecture support (amd64, arm64)

## Development Notes

### Database Handling
- Multiple WeChat versions supported with separate models
- SQLite databases are copied and decrypted locally
- Repository pattern abstracts database operations

### Media Processing
- Real-time image decryption during HTTP serving
- SILK audio format conversion to MP3
- LZ4 and ZSTD compression support

### Configuration
- Viper for configuration management
- Support for multiple data directories
- Account switching capabilities

## Testing

The project includes unit tests with coverage reporting. Run tests before submitting changes:

```bash
make test
# or
go test ./... -cover
```

## Linting

golangci-lint is used for code quality. Ensure code passes linting:

```bash
make lint
# or
golangci-lint run ./...
```