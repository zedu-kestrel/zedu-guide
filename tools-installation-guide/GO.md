# 🐹 Go (Golang) Setup Guide

Some backend microservices, API servers, and tools in the Zedu ecosystem (such as the `zedu-todo-list-app` or backend services) are built using **Go (Golang)**.

This guide walks you through installing Go, configuring your environment variables, running Go services, and resolving common network or module errors.

---

## 📌 Part 1: Installing Go

### 🪟 Windows
1. Go to the official Go downloads page: [go.dev/dl](https://go.dev/dl/).
2. Download the latest Windows installer (e.g., `go1.22.x.windows-amd64.msi` or higher).
3. Run the `.msi` installer and follow the wizard (default installation directory is `C:\Program Files\Go`).
4. Click **Finish**.
5. **Restart your terminal** (or VS Code) and verify:
   ```powershell
   go version
   ```
   *(You should see output like: `go version go1.22.x windows/amd64`)*

---

### 🍎 macOS

#### Option A: Using Homebrew (Recommended)
Open Terminal and run:
```bash
brew install go
```

#### Option B: Official Installer
1. Download the macOS package (`.pkg`) from [go.dev/dl](https://go.dev/dl/) (choose Apple Silicon `arm64` for M1/M2/M3/M4 or `amd64` for Intel).
2. Open and run the installer.
3. Restart Terminal and verify:
   ```bash
   go version
   ```

---

### 🐧 Linux (Ubuntu / Debian / WSL)

1. Remove any old versions and download the official archive:
   ```bash
   # Clean previous installation
   sudo rm -rf /usr/local/go

   # Download Go (replace with latest version from go.dev/dl)
   curl -OL https://go.dev/dl/go1.22.5.linux-amd64.tar.gz

   # Extract to /usr/local
   sudo tar -C /usr/local -xzf go1.22.5.linux-amd64.tar.gz
   rm go1.22.5.linux-amd64.tar.gz
   ```

2. Add Go to your system PATH:
   Add this line to `~/.bashrc` (or `~/.zshrc`):
   ```bash
   export PATH=$PATH:/usr/local/go/bin:$(go env GOPATH)/bin
   ```
   Apply changes:
   ```bash
   source ~/.bashrc
   go version
   ```

---

## 📌 Part 2: Understanding Go Modules (`go.mod`)

Modern Go uses **Go Modules** to manage dependencies (similar to `package.json` in Node.js). You do not need to set up complex GOPATH directory structures; you can work from any directory.

Key Go commands you will use:

| Command | What It Does |
| :--- | :--- |
| `go mod tidy` | Automatically downloads all required dependencies and removes unused ones. |
| `go run .` | Compiles and runs the current Go project directly. |
| `go build` | Compiles your code into an executable file (`.exe` on Windows). |
| `go test ./...` | Runs all unit and integration tests across the repository. |
| `go env` | Displays all Go environment variables. |

---

## 📌 Part 3: Running a Go Project

When working on a Go repository:

```bash
# 1. Navigate to the Go project folder
cd path/to/go-service

# 2. Download and verify modules
go mod download
go mod tidy

# 3. Start the application
go run main.go
# or
go run .
```

---

## ❓ Troubleshooting & Edge Cases (Q&A)

### Q1: `go: command not found` after installing
**Why it happens:** The Go binary path has not yet been registered in your active terminal session.  
**How to fix:**
1. Completely close all terminal windows and VS Code, then re-open them.
2. If on Windows, check your Environment Variables: Ensure `C:\Program Files\Go\bin` is listed under your System `Path`.
3. If on Mac/Linux, verify that `export PATH=$PATH:/usr/local/go/bin` exists in your `~/.zshrc` or `~/.bashrc`.

---

### Q2: `go: module ... dial tcp i/o timeout` (Proxy Connection Timeout)
**Why it happens:** In some regions or behind corporate/university firewalls, Google's default Go proxy (`proxy.golang.org`) may be blocked or slow.  
**How to fix:**
Direct Go to download dependencies straight from source repositories or use an alternate proxy:
```bash
# Option 1: Bypass the proxy completely
go env -w GOPROXY=direct

# Option 2: Use goproxy.io
go env -w GOPROXY=https://goproxy.io,direct
```
Then run `go mod tidy` again.

---

### Q3: `go.mod file not found in current directory or any parent directory`
**Why it happens:** You ran `go run` or `go test` from a directory that does not contain a `go.mod` file.  
**How to fix:**
Run `ls` or `dir` to verify you are in the correct folder where `go.mod` is located, then `cd` into that folder.

---

### Q4: Dependency checksum mismatch or corrupt module cache
**Why it happens:** Cached files were partially downloaded or corrupted during an interrupted download.  
**How to fix:**
Clean your Go module cache and re-download:
```bash
go clean -modcache
go mod tidy
```
