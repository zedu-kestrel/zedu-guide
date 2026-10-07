# 📱 Zedu Mobile — Complete Setup & Developer Guide

This guide is designed for **every contributor**—from beginners to experienced engineers. Follow this step-by-step walkthrough to clone, configure, build, and run the **Zedu Mobile** app (`zedu-mobile`) on your Android phone or emulator.

---

## ⚡ Quick Architecture Overview
* **Framework**: React Native 0.83 (Bare CLI, not Expo Go)
* **Languages**: TypeScript / JavaScript / Native C++ / Java / Kotlin
* **Package Manager**: **npm** (using `npm ci` — do not use pnpm or yarn for mobile!)
* **Target Platforms**: Android & iOS

---

## 🛠️ Step 0: Ensure Required Tools Are Installed

Before proceeding, confirm you have installed the required toolchain:
1. **Node.js 20+**
2. **Java JDK 17 (Temurin)** *(JDK 21 or 25 will fail builds!)*
3. **Android SDK (API Level 36, Build-Tools 36.0.0, NDK 28)**
4. **Android Platform-Tools (`adb`)**

👉 **Need to install these?** Follow our step-by-step **[React Native & Android Setup Guide](tools-installation-guide/REACT_NATIVE_AND_ANDROID.md)**.

Quick verification command in PowerShell / Terminal:
```powershell
java -version              # Must say openjdk 17.x
node -v                    # Must be v20.x+
adb --version              # Must display Android Debug Bridge version
```

---

## 🚀 Step 1: Forking & Cloning the Repository

We follow the standard HNG Forking Workflow:

1. **Fork the repository** on GitHub from `zedu-hng/zedu-mobile` into your team's GitHub organization (e.g., `zedu-kestrel/zedu-mobile`).
2. Clone your **team's fork** to your local machine:
   ```bash
   git clone https://github.com/zedu-kestrel/zedu-mobile.git
   cd zedu-mobile
   ```
3. Add the upstream review repository:
   ```bash
   git remote add upstream https://github.com/zedu-hng/zedu-mobile.git
   git fetch upstream
   ```

---

## 🔐 Step 2: Setting Up Your `.env` File

Zedu Mobile requires an environment file to communicate with the backend.

1. Create a `.env` file in the root of `zedu-mobile`:
   ```powershell
   New-Item -ItemType File -Name ".env" -Force
   ```
2. Populate `.env` with your team's backend configuration (see [env.d.ts](file:///c:/Users/hp/Documents/hng/zedu/zedu-mobile/env.d.ts)):
   ```env
   API_URL=https://api.staging.zedu.chat/api/v1/
   BASE_URL=https://api.staging.zedu.chat/
   CLIENT_URL=https://staging.zedu.chat/
   CONNECT_URL=wss://connect.staging.zedu.chat/connection/websocket
   CLIENT_ID=your_client_id
   GOOGLE_CLIENT_ID=your_google_client_id
   CLIENT_SECRET=your_client_secret
   ONESIGNAL_APP_ID=your_onesignal_id
   AGORA_APP_ID=your_agora_app_id
   ```

> [!WARNING]
> **Never commit your `.env` file!**  
> `.env` files are blocked by the CI security scanners. The repo's `.gitignore` already ignores it.

---

## 📦 Step 3: Installing Dependencies Safely

In `zedu-mobile`, **always use `npm ci`**:

```bash
npm ci
```

### Why `npm ci` and not `npm install` or `pnpm`?
* `npm ci` installs the exact dependencies recorded in `package-lock.json` without modifying the lockfile.
* It automatically triggers `patch-package` to apply necessary native code fixes.
* It initializes Husky git commit hooks to safeguard commit formatting.

---

## 🔌 Step 4: Connecting Your Android Device

You can use either your physical Android phone (recommended) or an Android Emulator.

### Method A: Physical Android Phone via USB (Easiest)
1. On your phone, go to **Settings ➔ About Phone ➔ tap "Build Number" 7 times** to unlock Developer Options.
2. Go to **Settings ➔ Developer Options** and toggle **USB Debugging** to **ON**.  
   *(Xiaomi/Redmi users: Also turn ON "Install via USB").*
3. Connect your phone with a USB data cable and select **File Transfer** mode.
4. When prompted on your phone screen with **"Allow USB debugging?"**, check **"Always allow"** and tap **Allow**.
5. Verify your PC sees the phone:
   ```powershell
   adb devices
   ```
   *Expected output:*
   ```text
   List of devices attached
   XXXXXXXXXX    device
   ```

### Method B: Virtual Android Emulator
1. Open Android Studio ➔ **Virtual Device Manager**.
2. Click **Play (▶️)** to launch your created AVD emulator.
3. Run `adb devices` to verify it appears as `emulator-5554 device`.

---

## 🏃 Step 5: Running the Mobile App

Running React Native requires **two terminals**:

### Terminal 1: Start the Metro Bundler
Metro compiles and serves your JavaScript code live to the phone:
```bash
cd zedu-mobile
npm start
```
Keep this terminal open.

---

### Terminal 2: Build and Install on Android
Open a **new terminal window** in the `zedu-mobile` directory:
```bash
cd zedu-mobile
npm run android
```

### What happens next?
* Gradle compiles the native Android app using JDK 17 and your Android SDK.
* The APK is installed directly onto your connected device or emulator.
* The Zedu app will automatically launch on your phone screen!
* Changes you make in `src/` will instantly update on your phone via **Fast Refresh**.

---

## 🧪 Step 6: Pre-Commit & PR Quality Checks

Before committing or pushing any code to a branch, run the local CI checks:

```bash
npm run check-format   # Verifies Prettier code style
npm run check-lint     # Runs ESLint checks
npm run check-types    # Runs TypeScript compiler check (tsc)
npm test               # Runs Jest unit tests
```

---

## 🛠️ Troubleshooting & Frequently Encountered Issues

### Issue 1: Gradle Build Fails with Java Compilation / Unsupported Class Version Error
* **Cause**: Your `JAVA_HOME` is pointing to JDK 21 or JDK 25 instead of JDK 17.
* **Fix**:
  1. Verify active Java version: `java -version`
  2. Point `JAVA_HOME` to JDK 17:
     ```powershell
     [Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Eclipse Adoptium\jdk-17.0.20.101-hotspot", "User")
     ```
  3. Restart your terminal.

---

### Issue 2: `adb devices` shows `unauthorized` or is Empty
* **Cause**: The phone is either not in debugging mode, using a charge-only cable, or the computer hasn't been authorized.
* **Fix**:
  1. Unplug and replug the USB cable.
  2. Unlock your phone screen.
  3. Look for the **"Allow USB debugging?"** dialog and tap **Allow**.
  4. Run `adb kill-server` followed by `adb devices`.

---

### Issue 3: Metro Bundler shows red error screen on phone or won't connect
* **Cause**: The phone cannot route port 8081 back to your PC.
* **Fix**: Reverse the port over ADB:
  ```powershell
  adb reverse tcp:8081 tcp:8081
  ```
  Then reload the app by pressing <kbd>R</kbd> twice on your phone keyboard or shaking the device to open the developer menu.

---

### Issue 4: `pnpm-workspace.yaml` or `package.json` Shows Modified in Git
* **Cause**: Accidentally modifying files or running package manager commands that alter workspace configuration.
* **Fix**:
  Discard unwanted changes to keep your branch pristine:
  ```powershell
  git checkout pnpm-workspace.yaml package.json package-lock.json
  ```
  Only commit the specific files your ticket requires.

---

### Issue 5: How to Test Without Installing Anything Locally
* If you cannot install the Android SDK on your PC, you can use the **GitHub Actions PR Build**:
  1. Open a PR from your ticket branch into `zedu-hng/zedu-mobile:dev`.
  2. In your fork on GitHub, go to **Actions ➔ PR build ➔ Run workflow** on your branch.
  3. Once the workflow completes, download the generated `android-apk` artifact.
  4. Copy `app-release.apk` to your Android phone and install it directly!
