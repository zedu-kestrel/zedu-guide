# 🟢 Node.js & pnpm Setup Guide

The Zedu frontend is built using **Next.js 15**, which requires **Node.js (version 20 or higher)** and uses **pnpm** as its fast, disk-efficient package manager.

This guide walks you through installing and configuring both tools across Windows, macOS, and Linux, including common Windows PowerShell permission traps.

---

## ⚠️ Important Warning Before You Start

> [!CAUTION]
> **DO NOT USE `npm install` OR `yarn`!**
> This repository strictly uses `pnpm` and maintains a `pnpm-lock.yaml` file.
> If you run `npm install`, you will generate a `package-lock.json` file. Pushing this file will immediately fail the automated CI checks.

---

## 📌 Part 1: Installing Node.js (v20+ LTS)

### 🪟 Windows

#### Option A: Official Installer (Easiest)
1. Go to [nodejs.org](https://nodejs.org/).
2. Download the **LTS (Long Term Support)** version (ensure it says version 20.x or 22.x).
3. Run the `.msi` installer.
4. Accept defaults (you can leave "Automatically install the necessary tools..." unchecked).
5. Open a **new** PowerShell or Command Prompt window and test:
   ```powershell
   node -v
   npm -v
   ```
   *(Should print `v20.x.x` or `v22.x.x`)*

#### Option B: Using NVM for Windows (For Advanced Users)
If you switch between different Node versions on your machine, download `nvm-setup.exe` from [github.com/coreybutler/nvm-windows/releases](https://github.com/coreybutler/nvm-windows/releases). Then run:
```powershell
nvm install 20
nvm use 20
```

---

### 🍎 macOS

#### Option A: Using Homebrew (Recommended)
Open Terminal and run:
```bash
brew install node@20
brew link --overwrite --force node@20
```

#### Option B: Official Installer
1. Go to [nodejs.org](https://nodejs.org/) and download the macOS installer (`.pkg`).
2. Run the installer, then restart Terminal.

Verify:
```bash
node -v
```

---

### 🐧 Linux (Ubuntu / Debian / WSL)
Open terminal and install Node 20 via NodeSource:
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
node -v
```

---

## 📌 Part 2: Installing pnpm

`pnpm` is the package manager that installs all libraries (React, Next.js, Tailwind, etc.) for Zedu.

### Method 1: Using Corepack (Built into Node.js — Recommended)
Node.js comes bundled with a tool called `corepack` that manages pnpm for you:

1. Open PowerShell / Terminal and run:
   ```bash
   corepack enable
   corepack prepare pnpm@latest --activate
   ```
2. Test installation:
   ```bash
   pnpm -v
   ```
   *(Should print `10.x.x` or `9.x.x`)*

---

### Method 2: Global npm Install (Fallback)
If Corepack fails or throws permission errors, run:
```bash
npm install -g pnpm
```

---

### Method 3: Standalone Shell Scripts
- **Windows (PowerShell):**
  ```powershell
  iwr https://get.pnpm.io/install.ps1 -useb | iex
  ```
- **macOS / Linux:**
  ```bash
  curl -fsSL https://get.pnpm.io/install.sh | sh -
  ```

---

## 📌 Part 3: Running the Project with pnpm

Once inside the `zedu-fe` folder:

```bash
# 1. Install all dependencies
pnpm install

# 2. Start the local development server
pnpm dev
```
Open your browser to [http://localhost:3000](http://localhost:3000) to view Zedu!

---

## ❓ Troubleshooting & Edge Cases (Q&A)

### Q1: On Windows PowerShell: `pnpm : File C:\...\pnpm.ps1 cannot be loaded because running scripts is disabled on this system.`
**Why it happens:** Windows blocks PowerShell scripts by default for security.  
**How to fix:**
1. Open PowerShell as Administrator (or in your regular terminal).
2. Run this command:
   ```powershell
   Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
   ```
3. Type `Y` (Yes) and press Enter.
4. Try running `pnpm -v` again. It will now work smoothly!

---

### Q2: I get `pnpm: command not found` after installing
**Why it happens:** Your terminal needs to be refreshed so it recognizes the newly added PATH environment variables.  
**How to fix:**
1. Close all open VS Code or terminal windows and open a new one.
2. If using standalone installer on Mac/Linux, run:
   ```bash
   source ~/.bashrc   # or source ~/.zshrc
   ```

---

### Q3: I accidentally ran `npm install` and now I have a `package-lock.json` file!
**Why it is a problem:** If you commit `package-lock.json`, the upstream PR CI bot will fail your PR.  
**How to fix:**
Delete the extra lock file and re-install with pnpm:
```bash
# Delete package-lock.json and node_modules
rm package-lock.json
rm -rf node_modules

# On Windows PowerShell:
Remove-Item -Force package-lock.json
Remove-Item -Recurse -Force node_modules

# Reinstall cleanly with pnpm
pnpm install
```

---

### Q4: When running `pnpm dev`, it fails with `Error: Unsupported Node.js version` or syntax error in Next.js
**Why it happens:** Your Node.js version is too old (e.g. Node 16 or 18). Next.js 15 requires Node 18.18+ or Node 20+.  
**How to check:**
```bash
node -v
```
If it prints less than `v20.0.0`, download and install Node 20 LTS from [nodejs.org](https://nodejs.org/).

---

### Q5: `ERR_PNPM_PEER_DEP_ISSUES` or lockfile warnings
**Why it happens:** Sometimes a new package adds dependencies with strict peer constraints.  
**How to fix:**
Run:
```bash
pnpm install --no-frozen-lockfile
```
Then run the verification commands:
```bash
pnpm run check-types
pnpm run build
```
