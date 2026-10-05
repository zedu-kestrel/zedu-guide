# 🦅 Team Kestrel — Zedu Engineering & Contribution Hub

Welcome to the official developer setup, contribution, and CI/CD guide for **Team Kestrel** on the **Zedu (HNG 15 Internship)** project!

Whether you are an experienced software engineer or a beginner collaborating with AI coding assistants (such as Antigravity, Cursor, Claude Code, or Copilot), this repository contains the complete step-by-step blueprints to develop, test, and ship your tasks without getting blocked by CI/CD review bots.

---

## 📚 Guide Index

### 🚀 Core Workflow Guides
| Document | Purpose |
| :--- | :--- |
| **[🎯 Task 3 Walkthrough Guide](TASK_3_WALKTHROUGH.md)** | **Start here for Task 3!** Step-by-step guide to updating text/name (no styling changes), testing, branching, and getting merged. |
| **[1. Getting Started & Setup](GETTING_STARTED.md)** | Cloning the correct fork, setting up remotes, Node/pnpm environment, and running locally. |
| **[2. Contribution & Git Workflow](CONTRIBUTING.md)** | Branch naming regex, syncing with upstream, conventional commit rules, and opening PRs. |
| **[3. CI/CD & Review Bots Blueprint](CI_CD_AND_BOT_RULES.md)** | Complete breakdown of PR review bots, hardcoded URL bans, docstring coverage, and fork build relays. |
| **[4. AI Prompting Cheatsheet](AI_PROMPTING_CHEATSHEET.md)** | Ready-to-copy prompts to give your AI pair programmer so it follows repo rules automatically. |

### 🛠️ Tool & Environment Setup Guides (Windows, Mac, Linux)
| Tool | Guide | Covers |
| :--- | :--- | :--- |
| **Git & GitHub** | **[Git & GitHub Guide](tools-installation-guide/GIT_AND_GITHUB.md)** | Installing Git, creating account, requesting access to `@zedu-kestrel`, accepting invites, and fixing **403 Permission Denied** errors. |
| **Node.js & pnpm** | **[Node.js & pnpm Guide](tools-installation-guide/NODE_AND_PNPM.md)** | Installing Node 20 LTS, pnpm 10.x, Corepack, and fixing Windows PowerShell script execution policy errors. |
| **Docker** | **[Docker Setup Guide](tools-installation-guide/DOCKER.md)** | Docker Desktop installation, WSL 2 on Windows, Mac Apple Silicon/Intel, Linux engine, port conflict resolution, and memory limits. |
| **Go (Golang)** | **[Go (Golang) Guide](tools-installation-guide/GO.md)** | Installing Go for backend microservices (`zedu-todo-list-app`), GOPROXY configuration, and modules. |

---

## ⚠️ The Big Stage 2 Update: Why You Must Re-Clone

In Stage 1, forks were taken from `zeduchat/zedu-fe`. **For Stage 2 and beyond, HNG created an official review repository:**

```text
Review Repository (Upstream):  https://github.com/zedu-hng/zedu-fe
Team Kestrel Fork (Origin):    https://github.com/zedu-kestrel/zedu-fe
```

> [!IMPORTANT]
> **Do not use the old `zeduchat` fork.**
> Team Kestrel's official fork is now **`zedu-kestrel/zedu-fe`**. Every team member should clone this repository and configure their remotes properly.

---

## ⚡ Golden Rules for Every Task

1. **Target Upstream Directly:** Every Pull Request must target `zedu-hng/zedu-fe` base branch `dev`. Never PR into `zedu-kestrel`'s own `dev` first (doing so violates the single-author rule when merged).
2. **Strict Branch Naming:** Every branch MUST follow:
   ```text
   feat/<ticket-or-task-id>-<description>
   fix/<ticket-or-task-id>-<description>
   ```
   *Example:* `feat/task-2-zedu-kestrel-contributors-page` *(Valid)*  
   *Counterexample:* `feature/contributor` *(Blocked by CI)*
3. **Conventional Commit & PR Title:** Always format commits and PR titles with your ticket ID inside the parenthesis:
   ```text
   feat(<ticket-id>): descriptive title in lowercase
   ```
   *Example:* `feat(KESTREL-001): add team kestrel contributors page`
4. **Zero Hardcoded URLs:** Never write `http://` or `https://` in frontend components unless it is `w3.org` or `avatars.githubusercontent.com`. External URLs trigger a blocking error in the PR Review Bot. Use `mailto:`, relative paths, or environment variables.
5. **Docstring Coverage:** Every new exported component and function must have a JSDoc block (`/** ... */`) so CodeRabbit gives 100% docstring coverage.
6. **Trigger Fork Build:** After opening your PR, navigate to your fork ➔ **Actions** ➔ **PR build** ➔ click **Run workflow** on your branch. Once it finishes green, comment `/fork-build` on your PR.

---

## 👥 Team Lead Review Gate
Our team lead on GitHub is:
**`@yvnks`**

Before any PR can be merged into Zedu, it must pass all automated CI gates and receive lead approval. Follow the templates in [`CONTRIBUTING.md`](CONTRIBUTING.md) to ensure instant reviews!
