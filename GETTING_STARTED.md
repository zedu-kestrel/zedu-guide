# 🚀 Getting Started & Local Environment Setup

This guide walks every Team Kestrel member through setting up a clean local development environment from scratch.

---

## 1. Prerequisites & Tool Installation

Before cloning, verify that you have the required tools installed on your computer. If you do not have them installed or need to configure them, follow our dedicated step-by-step guides:

* 🐙 **[Git & GitHub Setup Guide](tools/GIT_AND_GITHUB.md)** — Installing Git, GitHub account setup, requesting team access, accepting invitations, and fixing 403 permission errors.
* 🟢 **[Node.js & pnpm Setup Guide](tools/NODE_AND_PNPM.md)** — Installing Node 20 LTS, pnpm, and fixing Windows PowerShell script execution policy errors.
* 🐳 **[Docker Setup Guide](tools/DOCKER.md)** — Running databases and microservices with Docker Desktop (Windows WSL2, Mac Apple Silicon/Intel, Linux).
* 🐹 **[Go (Golang) Setup Guide](tools/GO.md)** — Installing Go for backend microservices, modules, and proxy settings.

### Quick Version Check Table:

| Tool | Recommended Version | Verify Command | Setup Guide |
| :--- | :--- | :--- | :--- |
| **Git** | `2.40+` | `git --version` | [Git Guide](tools/GIT_AND_GITHUB.md) |
| **Node.js** | `v20.x` (LTS) | `node -v` | [Node Guide](tools/NODE_AND_PNPM.md) |
| **pnpm** | `10.27.0` (matching repo) | `pnpm -v` | [pnpm Guide](tools/NODE_AND_PNPM.md) |
| **Docker** (Optional) | `26.x+` / Compose `v2.x` | `docker --version` | [Docker Guide](tools/DOCKER.md) |
| **Go** (Optional) | `1.22+` | `go version` | [Go Guide](tools/GO.md) |

> [!TIP]
> If you don't have `pnpm` installed, run:
> ```bash
> npm install -g pnpm@10.27.0
> ```
> *(On Windows, if you get a script permission error, see [Node & pnpm Guide Q1](tools/NODE_AND_PNPM.md#q1-on-windows-powershell-pnpm--file-cpnpmps1-cannot-be-loaded-because-running-scripts-is-disabled-on-this-system))*

---

## 2. Cloning the Repository & Remote Configuration

We work using a **Forking Workflow**. This means:
* **`origin`** is Team Kestrel's fork where you push your feature branches.
* **`upstream`** is the main review repository where Zedu codebase lives.

### Step 2.1: Clone Team Kestrel's Fork
In your terminal, navigate to your desired workspace folder and clone:

```bash
git clone https://github.com/zedu-kestrel/zedu-fe.git
cd zedu-fe
```

### Step 2.2: Add the Official Upstream Remote
Link the upstream HNG repository so you can sync the latest changes at any time:

```bash
git remote add upstream https://github.com/zedu-hng/zedu-fe.git
```

### Step 2.3: Verify Remotes
Run:
```bash
git remote -v
```

You should see:
```text
origin    https://github.com/zedu-kestrel/zedu-fe.git (fetch)
origin    https://github.com/zedu-kestrel/zedu-fe.git (push)
upstream  https://github.com/zedu-hng/zedu-fe.git (fetch)
upstream  https://github.com/zedu-hng/zedu-fe.git (push)
```

---

## 3. Installing Dependencies

Run:
```bash
pnpm install --frozen-lockfile
```

> [!NOTE]
> Always use `pnpm`, never `npm` or `yarn`. Using other package managers can corrupt the `pnpm-lock.yaml` file and cause CI security checks to fail.

---

## 4. Running the Project Locally

Start the local development server:

```bash
pnpm dev
```

Open your browser and navigate to:
```text
http://localhost:3000
```

To view Team Kestrel's contributors page:
```text
http://localhost:3000/contributors/zedu-kestrel
```

---

## 5. Local Verification Commands (Must Pass Before Committing!)

The repository has an automated pre-commit hook (`husky` + `lint-staged`) that runs tests before any commit is saved. To ensure your code passes without errors, run these three commands locally:

### 1. Code Formatting (Prettier)
```bash
pnpm run check-format
```
*If this reports formatting issues, auto-fix with:*
```bash
pnpm run format
```

### 2. Linting (ESLint)
```bash
pnpm run check-lint
```

### 3. TypeScript Type Checking
```bash
pnpm run check-types
```
*(This runs `tsc --pretty --noEmit` and ensures there are zero type errors).*

### 4. Production Build Test (Optional but recommended)
```bash
pnpm run build
```
*(Verifies that Next.js compile and static route generation succeed without issues).*

---

## Next Step
Proceed to **[`CONTRIBUTING.md`](CONTRIBUTING.md)** to learn the exact branch naming, commit syntax, and pull request workflow.
