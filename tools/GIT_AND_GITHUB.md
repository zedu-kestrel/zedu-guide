# 🐙 Git & GitHub Setup Guide

This guide will walk you through installing **Git**, creating and configuring your **GitHub account**, connecting your computer securely to GitHub, requesting access to **Team Kestrel**, and troubleshooting common issues (like permission errors and credential mix-ups).

---

## 📌 Part 1: Installing Git on Your Computer

Git is the program installed on your computer that tracks changes in code and allows you to download and upload files to GitHub.

### 🪟 Windows
1. Download the official installer from [git-scm.com/download/win](https://git-scm.com/download/win).
2. Choose **64-bit Git for Windows Setup**.
3. Run the installer `.exe`. During setup:
   - When asked about the default editor, you can pick **Visual Studio Code** or keep the default.
   - When asked about adjusting your PATH environment, pick **"Git from the command line and also from 3rd-party software"** (Recommended).
   - When asked about line ending conversions, choose **"Checkout Windows-style, commit Unix-style line endings"**.
   - For credential helper, choose **"Git Credential Manager"** (very important!).
4. Click **Finish**.
5. Open a new **PowerShell** or **Command Prompt** window and verify:
   ```powershell
   git --version
   ```
   *(You should see something like `git version 2.4x.x`)*

### 🍎 macOS
1. Open the **Terminal** app.
2. If you have Homebrew installed, run:
   ```bash
   brew install git
   ```
3. If you don't have Homebrew, simply type:
   ```bash
   git --version
   ```
   macOS will prompt you to install Apple's **Xcode Command Line Tools**. Click **Install** and let it finish.

### 🐧 Linux (Ubuntu / Debian / WSL)
Open your terminal and run:
```bash
sudo apt update
sudo apt install -y git
git --version
```

---

## 📌 Part 2: Setting Up Your Git Identity

Every time you commit code, Git stamps your name and email onto that change. This email **must match** your GitHub email so GitHub attributes your contributions to your profile.

In your terminal, run:
```bash
git config --global user.name "Your Full Name"
git config --global user.email "your-github-email@example.com"
```

Verify your setup:
```bash
git config --list
```

---

## 📌 Part 3: GitHub Account & Requesting Team Access

### 1. Create a GitHub Account (If you don't have one)
1. Go to [github.com/signup](https://github.com/signup).
2. Register with your preferred email address.
3. Choose a professional username (e.g. `john-doe` or `johndoe-dev`).

### 2. Request Access to Team Kestrel
To push branches and work on the Zedu frontend repository, you must be a member of the **`zedu-kestrel`** organization.

1. Find your **GitHub Username** (found in top right corner of GitHub when logged in).
2. Send your GitHub username to the team lead (**`@yvnks`**) or post it in the team Slack/Discord/WhatsApp channel requesting invite:
   > *"Hello Lead, please add my GitHub account `@your-username` to the `zedu-kestrel` organization so I can contribute."*
3. The lead or admin will send you an invitation.

### 3. Accepting the Invitation (CRITICAL STEP!)
> [!IMPORTANT]
> Simply being invited is NOT enough! You must **actively accept** the invitation before GitHub gives you permission to push code.

1. Check the email associated with your GitHub account for an email from GitHub with subject: *"@yvnks has invited you to join @zedu-kestrel"*. Click **Accept Invitation**.
2. **Alternatively**, log into GitHub in your browser and visit:
   👉 **`https://github.com/zedu-kestrel`** or **`https://github.com/zedu-kestrel/zedu-fe/invitations`**
3. Click the green button: **"Join zedu-kestrel"** or **"Accept invitation"**.

---

## 📌 Part 4: Authenticating Your Computer with GitHub

When you push code, GitHub needs to verify that you are who you say you are. GitHub no longer allows passwords in the terminal; you must use either **Git Credential Manager** (easiest) or **SSH keys**.

### Method A: Git Credential Manager (Easiest — Recommended)
When you run `git push` for the first time:
1. A browser window or pop-up will automatically open asking: *"Sign in to GitHub"*.
2. Click **"Sign in with your browser"**.
3. Authorize Git.
4. Git will securely save your token in your Windows Credential Manager or macOS Keychain. You won't have to sign in again!

### Method B: Using an SSH Key (For Mac, Linux, or Advanced Users)
1. In your terminal, generate a key:
   ```bash
   ssh-keygen -t ed25519 -C "your-github-email@example.com"
   ```
   *(Press Enter to accept default location, press Enter twice for no passphrase)*
2. Start the SSH agent and add your key:
   ```bash
   # On Mac / Linux:
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```
3. Copy the public key:
   ```bash
   # On Windows PowerShell:
   Get-Content ~/.ssh/id_ed25519.pub | Set-Clipboard

   # On Mac:
   pbcopy < ~/.ssh/id_ed25519.pub

   # On Linux:
   cat ~/.ssh/id_ed25519.pub
   ```
4. Go to GitHub ➔ **Settings** (click your profile avatar top right) ➔ **SSH and GPG keys** ➔ click **New SSH Key**.
5. Title: `My Laptop`, Key: Paste the content, click **Add SSH key**.
6. Test connection:
   ```bash
   ssh -T git@github.com
   ```
   *(You should see: "Hi username! You've successfully authenticated...")*

---

## ❓ Troubleshooting & Edge Cases (Q&A)

### Q1: I get `remote: Permission to zedu-kestrel/zedu-fe.git denied to <username>. fatal: unable to access ... The requested URL returned error: 403` when pushing.
**Why it happens:**
1. You have not accepted the invitation to `zedu-kestrel`. Visit `https://github.com/zedu-kestrel/zedu-fe/invitations` and click Accept.
2. The team lead hasn't granted "Write" permission to org members or your team.
3. Your computer is logged into a different personal GitHub account in Windows Credential Manager.

**How to fix credential mismatch on Windows:**
1. Open Windows Search and type **"Credential Manager"**.
2. Click **Windows Credentials**.
3. Look under *Generic Credentials* for `git:https://github.com`.
4. Click **Remove**.
5. Back in your terminal, run `git push` again. A popup will ask you to log into GitHub in your browser. Sign into the correct GitHub account!

---

### Q2: I get `fatal: not a git repository (or any of the parent directories): .git`
**Why it happens:** You ran a git command (like `git status` or `git pull`) outside the project folder.  
**How to fix:** Navigate inside the cloned folder first:
```bash
cd zedu-fe
git status
```

---

### Q3: Git says `fatal: remote upstream already exists`
**Why it happens:** You already ran `git remote add upstream ...` before.  
**How to check:**
```bash
git remote -v
```
If you see `upstream` pointing to `https://github.com/zedu-hng/zedu-fe.git`, you are already configured and good to go!

---

### Q4: I committed using the wrong email and GitHub doesn't show my profile on the commit
**Why it happens:** Your local `git config user.email` does not match the email registered on your GitHub account.  
**How to fix:**
```bash
git config --global user.email "your-exact-github-email@example.com"
```
Also check in GitHub: **Settings ➔ Emails** to make sure that email address is verified.

---

### Q5: Git tells me `fatal: refusing to merge unrelated histories` when pulling
**Why it happens:** You cloned an empty repo or different fork and tried to pull another repo's branch.  
**How to fix:** Delete the folder and follow the exact clone instructions in [GETTING_STARTED.md](../GETTING_STARTED.md) to clone `https://github.com/zedu-kestrel/zedu-fe.git`.
