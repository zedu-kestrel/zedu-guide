# 📱 React Native & Android SDK Setup Guide (Windows, Mac, Linux)

This guide provides a beginner-friendly, step-by-step walkthrough for installing and configuring all tools required to run **Zedu Mobile** (`zedu-mobile`).

---

## 📋 Required Tools Overview

| Tool | Required Version | Why It Is Needed |
| :--- | :--- | :--- |
| **Node.js** | `v20.x` or higher (LTS) | Runs the JavaScript engine and Metro bundler. |
| **Java JDK** | **JDK 17 (Temurin)** ⚠️ | Compiles Android Gradle code. *(JDK 21 or 25 will fail builds!)* |
| **Android SDK** | API Level 36 (`android-36`) | The target Android platform version. |
| **Android Build-Tools**| `36.0.0` | Packages the APK files. |
| **Android NDK** | `28.0.13004108` | Compiles native C++ libraries (Agora, Reanimated). |
| **CMake** | `3.22.1` | Native Android build system. |
| **Platform-Tools (`adb`)**| Latest | Communicates with your Android phone/emulator over USB. |

---

## 1. Installing Node.js

Zedu Mobile requires Node 20 or higher.

### Windows
1. Open PowerShell and run:
   ```powershell
   winget install OpenJS.NodeJS.LTS
   ```
2. Verify:
   ```powershell
   node -v
   npm -v
   ```
   *(Ensure Node is v20.x or v22.x+)*

---

## 2. Installing Java JDK 17 (Eclipse Adoptium Temurin)

> [!CAUTION]
> **Do NOT use JDK 21 or JDK 25!**  
> Gradle 9.0 in this project fails when building with Java versions higher than 17. You MUST use **JDK 17**.

### Windows Installation:
1. Run in PowerShell:
   ```powershell
   winget install EclipseAdoptium.Temurin.17.JDK
   ```
2. Set your `JAVA_HOME` environment variable:
   ```powershell
   [Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Eclipse Adoptium\jdk-17.0.20.101-hotspot", "User")
   ```
   *(Adjust folder name if your patch version differs; check `C:\Program Files\Eclipse Adoptium`)*

3. Verify:
   Close and reopen PowerShell, then run:
   ```powershell
   java -version
   ```
   Output **must** start with `openjdk version "17...`.

---

## 3. Setting Up the Android SDK

You have two choices:
* **Option A (Recommended for beginners)**: Install **Android Studio** (includes a visual emulator and UI).
* **Option B (Lightweight)**: Install only the **Android Command-line Tools** (uses less disk space, great if testing on a physical phone).

### Option A: Installing Android Studio & SDK

1. Install Android Studio:
   ```powershell
   winget install Google.AndroidStudio
   ```
   *(Follow the installer prompts and click Next/Finish).*
2. Open Android Studio from your Start menu.
3. On the welcome screen, click **More Actions** (three dots) ➔ **SDK Manager**.
4. Under the **SDK Platforms** tab:
   - Check **Android 15.0 ("VanillaIceCream") / API 36**.
5. Under the **SDK Tools** tab:
   - Check **Android SDK Build-Tools 36** ➔ click *Show Package Details* and check **36.0.0**.
   - Check **NDK (Side by side)** ➔ click *Show Package Details* and check **28.0.13004108**.
   - Check **CMake** ➔ check **3.22.1**.
   - Check **Android SDK Platform-Tools**.
   - Check **Android SDK Command-line Tools (latest)**.
6. Click **Apply** and accept the license agreements.

---

### Option B: Installing SDK via Command Line (No Android Studio)

If you only want command-line tools without the full IDE:

1. Create the Android SDK directory:
   ```powershell
   $sdk = "$env:LOCALAPPDATA\Android\Sdk"
   New-Item -ItemType Directory -Force "$sdk\cmdline-tools"
   ```
2. Download official command-line tools:
   ```powershell
   Invoke-WebRequest "https://dl.google.com/android/repository/commandlinetools-win-13114758_latest.zip" -OutFile "$env:TEMP\cmdtools.zip" -UseBasicParsing
   Expand-Archive "$env:TEMP\cmdtools.zip" "$sdk\cmdline-tools\_x" -Force
   Move-Item "$sdk\cmdline-tools\_x\cmdline-tools" "$sdk\cmdline-tools\latest"
   Remove-Item "$sdk\cmdline-tools\_x", "$env:TEMP\cmdtools.zip" -Recurse -Force
   ```
3. Accept licenses and install exact project packages:
   ```powershell
   $sm = "$sdk\cmdline-tools\latest\bin\sdkmanager.bat"
   (1..30 | % {'y'}) | & $sm --licenses --sdk_root=$sdk
   & $sm --sdk_root=$sdk "platform-tools" "platforms;android-36" "build-tools;36.0.0" "ndk;28.0.13004108" "cmake;3.22.1"
   ```

---

## 4. Configuring Android Environment Variables

To allow terminal commands (`adb`, `react-native`, `npm run android`) to locate the SDK:

1. In PowerShell, run:
   ```powershell
   $sdk = "$env:LOCALAPPDATA\Android\Sdk"
   [Environment]::SetEnvironmentVariable("ANDROID_HOME", $sdk, "User")

   # Add adb and command-line tools to your User PATH
   $up = [Environment]::GetEnvironmentVariable("Path", "User")
   $newPaths = @("$sdk\platform-tools", "$sdk\cmdline-tools\latest\bin") | Where-Object { $up -notlike "*$_*" }
   if ($newPaths) {
       [Environment]::SetEnvironmentVariable("Path", ($up.TrimEnd(';') + ";" + ($newPaths -join ';')), "User")
   }
   ```
2. **Restart your terminal** or VS Code for environment changes to take effect.
3. Verify `adb`:
   ```powershell
   adb --version
   ```

---

## 5. Connecting a Test Device

You need either a **physical Android phone** or a **virtual emulator**.

### Method 1: Using Your Physical Android Phone (Easiest)

1. On your Android phone, go to **Settings ➔ About phone**.
2. Find **Build number** (on Xiaomi/Tecno, look under *Software info* or tap *MIUI version*).
3. **Tap "Build number" 7 times** until you see a message saying *"You are now a developer!"*.
4. Go back to **Settings ➔ Developer options**:
   - Turn **USB debugging** to **ON**.
   - *(Xiaomi/Redmi/Poco only)*: Also turn **Install via USB** to **ON**.
5. Connect your phone to your PC using a **USB data cable** (ensure it's not a charge-only cable).
6. Set the USB connection mode on your phone to **File Transfer / MTP**.
7. A prompt will appear on your phone screen: **"Allow USB debugging?"**.  
   Check **"Always allow from this computer"** and tap **Allow**.
8. In your PC terminal, run:
   ```powershell
   adb devices
   ```
   You should see:
   ```text
   List of devices attached
   XXXXXXXXXX    device
   ```
   *(If it says `unauthorized`, unlock your phone screen and tap "Allow" on the prompt).*

---

### Method 2: Using an Android Emulator (AVD)

1. Open **Android Studio**.
2. Click **More Actions ➔ Virtual Device Manager** (or Tools ➔ Device Manager).
3. Click **Create Virtual Device**.
4. Select **Phone ➔ Pixel 7** (or any modern phone) and click **Next**.
5. Under system image, download and select **API 34 or 35 (x86_64)**.
6. Click **Finish**, then click the **Play (▶️)** button to start the emulator.

---

## 6. Verification Checklist

Run this quick command to verify everything is ready:

```powershell
java -version              # Must be 17.x
node -v                    # Must be 20.x+
adb devices                # Must list your phone or emulator
echo $env:ANDROID_HOME     # Must point to your Sdk folder
```

Now you are ready to follow the **[Mobile Setup Guide](../MOBILE_SETUP_GUIDE.md)**!
