# 🖥️ Zedu Desktop — Complete Setup & Developer Guide

This guide is designed for **every contributor**—from beginners to experienced engineers. Follow this step-by-step walkthrough to clone, configure, build, and run the **Zedu Desktop** app (`zedu-desktop`) on Windows.

---

## ⚡ Quick Architecture Overview
* **Framework**: Flutter 3.41.5 (Dart 3.11)
* **Architecture**: Clean Architecture (Presentation ➔ Domain ➔ Data) with Riverpod & GetIt
* **Target Desktop Platforms**: Windows (MSVC C++), macOS, Linux

---

## 🛠️ Step 0: Ensure Required Tools Are Installed

Before proceeding, confirm you have installed:
1. **Flutter SDK 3.41.5 (Stable channel)** located at `C:\src\flutter` (and in your `PATH`)
2. **Visual Studio Community** with the **"Desktop development with C++"** workload & Windows 10/11 SDK
3. **Microsoft C++ Compiler Flag** configured in your environment

👉 **Need to install these?** Follow our step-by-step **[Flutter & Desktop Setup Guide](tools-installation-guide/FLUTTER_AND_DESKTOP.md)**.

Quick verification in PowerShell:
```powershell
flutter doctor -v
```
Ensure you have green checkmarks for **Windows Version**, **Visual Studio**, and **Connected device (Windows desktop)**.

---

## 🚀 Step 1: Forking & Cloning the Repository

1. **Fork the repository** on GitHub from `zedu-hng/zedu-desktop` into your team's GitHub organization (e.g., `zedu-kestrel/zedu-desktop`).
2. Clone the **team's fork** locally:
   ```bash
   git clone https://github.com/zedu-kestrel/zedu-desktop.git
   cd zedu-desktop
   ```
3. Add the upstream review repository:
   ```bash
   git remote add upstream https://github.com/zedu-hng/zedu-desktop.git
   git fetch upstream
   ```

---

## 🔐 Step 2: Creating Your Local `.env` File

> [!IMPORTANT]
> The project's `pubspec.yaml` specifies `.env` under its bundled assets. If the `.env` file does not exist, the build will fail with a missing asset error.

Create `.env` by copying the template file:
```powershell
Copy-Item .env.example .env
```

If testing with live backend endpoints or Google Sign-In, update the variables in `.env`:
```env
API_BASE_URL=https://api.staging.zedu.chat/api/v1/
USE_MOCK_DATA=false
APP_FLAVOR=development
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
```

---

## 📦 Step 3: Installing Dependencies Cleanly

To preserve `pubspec.lock` and prevent accidental diffs, run:

```powershell
flutter pub get --enforce-lockfile
```

> [!TIP]
> The `--enforce-lockfile` flag ensures Flutter strictly respects the existing lockfile and does NOT change transitive dependencies. If `pubspec.lock` ever shows changes in `git status`, simply run `git checkout pubspec.lock` to discard them.

---

## 🔧 Step 4: First-Time Windows C++ Fixes (One-Time Setup)

Windows builds use CMake and native C++ compilers. Two one-time configuration steps prevent common build errors:

### Fix A: Silence MSVC Coroutine Warning (Modern Visual Studio)
Modern Visual Studio MSVC compilers show a fatal `error STL1011` when compiling older coroutine headers in third-party plugins. Set this machine-level environment variable:
```powershell
[Environment]::SetEnvironmentVariable("CL", "/D_SILENCE_EXPERIMENTAL_COROUTINE_DEPRECATION_WARNINGS", "User")
$env:CL = "/D_SILENCE_EXPERIMENTAL_COROUTINE_DEPRECATION_WARNINGS"
```
*(This tells the C++ compiler to allow the build without modifying any tracked files in the repo).*

### Fix B: Pre-Extract Agora SDK Binaries (Symlink Extraction Fix)
The `agora_rtc_engine` Flutter plugin contains a CMake script that attempts to extract native DLLs through a Windows directory symlink, which CMake's `tar` blocks.

Run this quick one-time PowerShell script to unpack them directly into your pub cache:
```powershell
$base = "$env:LOCALAPPDATA\Pub\Cache\hosted\pub.dev\agora_rtc_engine-6.5.4\windows"
$irisZip = "$base\third_party\iris\iris_4.5.3-build.1_DCG_Windows_Video_Standalone_20260428_1050_31959.zip"
$irisLib = "$base\third_party\iris\lib"
New-Item -ItemType Directory -Path $irisLib -Force | Out-Null
tar -xzf $irisZip -C $irisLib

$nativeDir = "$base\third_party\native"
$nativeLib = "$nativeDir\lib"
New-Item -ItemType Directory -Path $nativeLib -Force | Out-Null
$nativeUrl = "https://download.agora.io/sdk/release/Agora_Native_SDK_for_Windows_rel.v4.5.3.70_32091_FULL_20260416_1933_1076734.zip"
$nativeZip = "$nativeDir\Agora_Native_SDK_for_Windows_rel.v4.5.3.70_32091_FULL_20260416_1933_1076734.zip"
if (-not (Test-Path $nativeZip)) {
    Invoke-WebRequest -Uri $nativeUrl -OutFile $nativeZip -UseBasicParsing
}
tar -xzf $nativeZip -C $nativeLib

New-Item -ItemType File -Path "$base\.plugin_dev" -Force | Out-Null
```
*(This sets the `.plugin_dev` flag, instructing CMake to safely use the pre-extracted binaries).*

---

## 🏃 Step 5: Running the Desktop App

From the `zedu-desktop` root directory, launch the Windows application:

```powershell
flutter run -d windows
```

### What to Expect:
1. On the very first run, CMake will generate build files and MSBuild will compile all native plugins (`agora`, `window_manager`, `audioplayers`, etc.). This initial compile takes 2–4 minutes.
2. The **Zedu Desktop** window will pop up automatically on your screen!
3. Flutter's interactive console is now live:
   * Press <kbd>r</kbd> for **Hot Reload** (updates UI in milliseconds).
   * Press <kbd>R</kbd> for **Hot Restart**.
   * Press <kbd>q</kbd> to quit and close the app.

---

## 💡 What Is That "Select Solutions to Load" VS Code Popup?

When building for Windows, CMake generates temporary `.slnx` solution files in `build\windows\x64`. If you have the **C# Dev Kit** extension installed in VS Code, a modal will pop up:
> *"Select solutions to load in C# Dev Tools"*

### What to do:
* **Press <kbd>Esc</kbd>** or click **OK** with 0 items selected.
* Zedu is a Flutter (Dart/C++) app, not a .NET/C# project. You do **not** need to load any solution in C# Dev Kit.

---

## 🧪 Step 6: Pre-Commit & Quality Checks

Run these commands before opening any pull request:

```powershell
dart format lib test    # Automatically formats Dart code
flutter analyze         # Runs static analysis (must report "No issues found!")
flutter test            # Runs unit and widget tests
```

---

## 🛡️ Step 7: Keeping Your Repository 100% Clean

In a shared team repository, **never commit local build noise or accidental lockfile diffs**:

1. **Check your status**:
   ```powershell
   git status
   ```
2. **If you see unintended changes to `pubspec.lock` or `generated_*` files**:
   ```powershell
   git checkout pubspec.lock windows/ linux/ macos/
   ```
3. **Stage only the files you touched for your ticket**:
   ```powershell
   git add lib/features/auth/...
   git commit -m "feat(TICKET-123): implement user login flow"
   ```
