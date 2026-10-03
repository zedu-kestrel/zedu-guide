# 🐳 Docker Setup Guide

Docker allows you to run software (such as databases like PostgreSQL, caches like Redis, and backend microservices) inside lightweight, isolated packages called **containers**—without needing to install and configure complex servers directly on your operating system.

This guide explains how to install and configure Docker on **Windows**, **macOS**, and **Linux**, plus solutions to frequent edge cases like virtualization issues and port conflicts.

---

## 📌 Part 1: Installation by Operating System

### 🪟 Windows (Docker Desktop with WSL 2)

#### 1. Enable Hardware Virtualization in BIOS (Prerequisite)
1. Open Task Manager (`Ctrl + Shift + Esc`).
2. Go to the **Performance** tab and click **CPU**.
3. In the bottom-right corner, check **Virtualization:**. It must say **Enabled**.
   *(If disabled, you will need to enter your computer's BIOS/UEFI settings upon restart and enable Intel VT-x or AMD SVM).*

#### 2. Install / Update WSL 2 (Windows Subsystem for Linux)
Open **PowerShell as Administrator** and run:
```powershell
wsl --install
wsl --update
```
Restart your computer if prompted.

#### 3. Download and Install Docker Desktop
1. Download Docker Desktop for Windows from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/).
2. Run the installer `.exe`.
3. Ensure the option **"Use WSL 2 instead of Hyper-V"** is checked.
4. Once installation is complete, restart your PC.
5. Launch Docker Desktop from your Start menu and accept the terms.

---

### 🍎 macOS (Docker Desktop for Mac)

1. Check your Mac chip: Click the Apple icon  ➔ **About This Mac**.
   - If it says **Apple M1 / M2 / M3 / M4**, download the **"Mac with Apple silicon"** installer.
   - If it says **Intel**, download the **"Mac with Intel chip"** installer.
2. Download from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/).
3. Double-click `Docker.dmg`, drag the Docker whale icon into your **Applications** folder.
4. Open Docker from Applications and complete initial setup.

*(Note for Apple Silicon: If running older x86 containers, run `softwareupdate --install-rosetta` in Terminal).*

---

### 🐧 Linux (Docker Engine on Ubuntu / Debian)

On Linux, you generally install the native Docker Engine without a heavy GUI:

```bash
# 1. Remove conflicting packages
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

# 2. Add Docker's official GPG key and repository
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 3. Install Docker Engine and Docker Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 4. Allow your user to run Docker without 'sudo'
sudo usermod -aG docker $USER
```
> [!NOTE]
> Log out and log back in (or restart your terminal) for the group change to take effect.

---

## 📌 Part 2: Verifying Your Docker Installation

Open your terminal (PowerShell, Command Prompt, or Terminal) and run:

```bash
docker --version
docker compose version
docker run hello-world
```

If you see:
```text
Hello from Docker!
This message shows that your installation appears to be working correctly.
```
Your Docker environment is 100% operational!

---

## 📌 Part 3: Useful Commands for Day-to-Day Work

| Command | What It Does |
| :--- | :--- |
| `docker compose up -d` | Starts all services defined in `docker-compose.yml` in background. |
| `docker compose down` | Stops and removes running containers. |
| `docker compose ps` | Lists all running containers and their exposed ports. |
| `docker compose logs -f` | Streams live logs from all containers. |
| `docker system prune` | Cleans up stopped containers, unused networks, and dangling images to free disk space. |

---

## ❓ Troubleshooting & Edge Cases (Q&A)

### Q1: `error during connect: This error may indicate that the docker daemon is not running.`
**Why it happens:** The Docker Desktop application is closed, still starting up, or the service crashed.  
**How to fix:**
1. Open the **Docker Desktop** application from your desktop/apps menu.
2. Wait until the whale icon in the taskbar/status bar stops animating and turns solid green (showing *"Engine running"*).
3. Try your command again.

---

### Q2: `bind: address already in use` (Port Conflict)
**Why it happens:** Another application on your computer is already using the port (for example, port `3000`, `5432` for PostgreSQL, or `8080`).  
**How to fix on Windows:**
1. Find what process is using the port (e.g., port 5432):
   ```powershell
   netstat -ano | findstr :5432
   ```
2. Note the PID (the number at the far right).
3. Stop that process:
   ```powershell
   taskkill /PID <PID> /F
   ```
**How to fix on Mac / Linux:**
```bash
sudo lsof -i :5432
# Kill the PID shown:
kill -9 <PID>
```

---

### Q3: On Windows: `WSL 2 installation is incomplete` or `kernel component update required`
**Why it happens:** Windows has WSL enabled, but lacks the latest Linux kernel package.  
**How to fix:**
1. Open PowerShell as Administrator.
2. Run:
   ```powershell
   wsl --update
   wsl --shutdown
   ```
3. Restart Docker Desktop.

---

### Q4: Docker on Windows is consuming too much RAM (`vmmem` process)
**Why it happens:** WSL 2 dynamically allocates RAM up to 50% or more of your total system memory if not capped.  
**How to fix:**
1. Open Notepad and create a file at: `C:\Users\<YourUsername>\.wslconfig`
2. Add this configuration to cap RAM at 4GB:
   ```ini
   [wsl2]
   memory=4GB
   processors=2
   ```
3. In PowerShell, restart WSL:
   ```powershell
   wsl --shutdown
   ```
4. Start Docker Desktop again.

---

### Q5: On Linux: `Got permission denied while trying to connect to the Docker daemon socket`
**Why it happens:** Your Linux user account was not added to the `docker` user group.  
**How to fix:**
```bash
sudo usermod -aG docker $USER
newgrp docker
```
