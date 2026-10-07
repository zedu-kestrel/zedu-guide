# 🖥️ Flutter & Desktop SDK Setup Guide (Windows, Mac, Linux)

This guide provides a step-by-step walkthrough for installing and configuring all tools required to run **Zedu Desktop** (`zedu-desktop`).

---

## 📋 Required Tools Overview

| Tool | Required Version | Why It Is Needed |
| :--- | :--- | :--- |
| **Flutter SDK** | `3.41.5` (Stable Channel) | The cross-platform UI framework and Dart SDK compiler. |
| **Visual Studio Community** | VS 2022 / 2026 with **C++ Desktop Workload** | Microsoft C++ compiler (`cl.exe`), CMake, and MSBuild required to compile native Windows desktop binaries. |
| **Windows 10/11 SDK** | `10.0.22000+` or `10.0.26100.0` | Header files and Windows APIs for desktop window management and media. |
| **Git** | `2.40+` | Version control and channel management for Flutter. |

---

## 1. Installing Visual Studio & C++ Desktop Workload

To compile Flutter for Windows desktop, Microsoft Visual Studio with C++ tools is mandatory.

### Installation Steps:
1. Open PowerShell and run:
   ```powershell
   winget install Microsoft.VisualStudio.2022.Community --override "--passive --config https://aka.ms/vs/workloads/native-desktop"
   ```
2. Alternatively, download the installer from [Visual Studio Downloads](https://visualstudio.microsoft.com/downloads/):
   - In the Visual Studio Installer, ensure the workload **"Desktop development with C++"** is checked.
   - Verify that the **Windows 10 or 11 SDK** is checked in the right-side summary.
   - Click **Install**.

---

## 2. Installing the Flutter SDK (Pinned Version 3.41.5)

Zedu Desktop is developed against Flutter 3.41.5. To avoid version mismatches and lockfile diffs, install this exact version.

### Installation Steps (Windows):
1. Create a clean root directory (e.g., `C:\src`):
   ```powershell
   New-Item -ItemType Directory -Path "C:\src" -Force
   ```
2. Clone the Flutter SDK repository at tag `3.41.5`:
   ```powershell
   git clone --depth 1 --branch 3.41.5 https://github.com/flutter/flutter.git C:\src\flutter
   ```
3. Add Flutter to your User `PATH`:
   ```powershell
   $up = [Environment]::GetEnvironmentVariable("Path", "User")
   if ($up -notlike "*C:\src\flutter\bin*") {
       [Environment]::SetEnvironmentVariable("Path", ($up.TrimEnd(';') + ";C:\src\flutter\bin"), "User")
   }
   ```
4. **Restart PowerShell** or VS Code so the new PATH is active.

---

## 3. Configuring Windows C++ Compiler Environment Variable

> [!IMPORTANT]
> **Essential Fix for Modern Visual Studio (2022 / 2026):**  
> Modern Visual Studio MSVC compilers show a fatal compilation error (`error STL1011: The /await compiler option ... are deprecated`) when compiling third-party coroutine plugins like `audioplayers_windows`.  
> Rather than modifying tracked repository files, set this Microsoft C++ compiler environment variable on your machine:

Run this once in PowerShell:
```powershell
[Environment]::SetEnvironmentVariable("CL", "/D_SILENCE_EXPERIMENTAL_COROUTINE_DEPRECATION_WARNINGS", "User")
```
This tells Visual Studio to safely suppress the deprecation error during desktop builds across all projects without modifying any code.

---

## 4. Enabling Windows Developer Mode (Optional but Recommended)

Windows requires Developer Mode to allow non-administrator accounts to create file symlinks (which Flutter plugins use).

1. Open Windows **Settings** (press `Win + I`).
2. Navigate to **System ➔ For developers** (or Privacy & Security ➔ For developers).
3. Toggle **Developer Mode** to **ON**.

---

## 5. Verifying with `flutter doctor`

Run the Flutter diagnostic tool:
```powershell
flutter doctor -v
```

### Expected Output:
```text
[√] Windows Version (11 Pro 64-bit)
[√] Visual Studio - develop Windows apps (Visual Studio Community)
    • Windows SDK version 10.0.xxxxx
[√] Connected device (Windows desktop available)
[√] Network resources available
```

If you see green checkmarks for Windows Version, Visual Studio, and Connected devices, your toolchain is 100% ready!

Proceed to the **[Desktop Setup Guide](../DESKTOP_SETUP_GUIDE.md)** to clone and run the application.
