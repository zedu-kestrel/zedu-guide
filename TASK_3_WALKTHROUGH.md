# 🎯 Task 3 Step-by-Step Walkthrough Guide

This guide is designed for **every member of Team Kestrel**. It will walk you through completing **Task 3** from start to finish without getting stuck or blocked by automated CI review bots.

---

## 🛑 Before You Begin: Have You Set Up the Project?

If you have **not yet set up your computer or cloned the repository**, you must complete the setup first!

👉 **Read & Follow the [Getting Started & Setup Guide](GETTING_STARTED.md) first to:**
1. Install **Git, Node.js (v20+), and pnpm** (see [Tools Installation Guides](tools-installation-guide/)).
2. Ensure you have been invited to the **`@zedu-kestrel`** GitHub organization and accepted the invite (see [Git & GitHub Access Guide](tools-installation-guide/GIT_AND_GITHUB.md)).
3. Clone the official fork (`git clone https://github.com/zedu-kestrel/zedu-fe.git`).
4. Link the official upstream (`git remote add upstream https://github.com/zedu-hng/zedu-fe.git`).
5. Run `pnpm install` and verify the app opens on `http://localhost:3000`.

*Once you have completed the setup and the app runs on your computer, jump right into the steps below!*

---

## 📋 What is Task 3?

Task 3 requires every contributor to:
1. Make a **small, safe text change** somewhere in the Zedu application.
2. **STRICT RULE: DO NOT CHANGE ANY STYLING OR CSS.** Only change text.
3. Follow the team's Git branching and commit rules.
4. Open a Pull Request targeting upstream (`zedu-hng/zedu-fe:dev`).
5. Pass all automated CI/CD checks and get merged.

---

## 💡 Where & What Text Should You Change?

### 🌟 Option A (Recommended & Safest): Update Your Name / Text on Team Kestrel's Page
Our team has our own isolated contributors page at `src/app/(homepage)/contributors/zedu-kestrel/`. Modifying your own text here will **never conflict with other HNG teams**!

* **File to edit:**  
  `src/app/(homepage)/contributors/zedu-kestrel/_lib/contributors.ts`

* **Examples of approved text changes:**
  1. **Add your middle name:**  
     `name: "John Doe"` ➔ `name: "John Michael Doe"`
  2. **Add your known name / nickname in parentheses:**  
     `name: "Daniel Ifeanyi"` ➔ `name: "Daniel (Danny) Ifeanyi"`
  3. **Update your known display name or title:**  
     `username: "Global"` ➔ `username: "Global (QA Lead)"`  
     or `background: "Frontend Developer"` ➔ `background: "Frontend Engineer"`
  4. **If you are not yet in `contributors.ts`:** Add your contributor object using existing styling gradients without modifying CSS components.

> [!CAUTION]
> ### 🚫 NO STYLING CHANGES ALLOWED
> * **DO NOT** edit Tailwind classes (e.g. `bg-`, `text-`, `p-`, `flex`, `grid`).
> * **DO NOT** modify component styling files (like `ContributorCard.tsx` or `page.tsx`).
> * **DO NOT** change `avatarGradient` values.
> * **DO NOT** insert external `https://` URLs (keep LinkedIn as handle slug only, e.g. `"john-doe"`, NOT `"https://linkedin.com/..."`).

---

### 📝 Option B: A Minor Text Copy Improvement Elsewhere
If you choose to update a piece of text elsewhere in the application:
* Fix a typo, fix grammatical casing (e.g. small case to Title Case), or adjust a phrase to its nearest natural meaning.
* **Do NOT change any HTML tags or CSS classes.**
* **Do NOT change layout structure or core application logic.**

---

## 🚀 Step-by-Step Execution Plan

### Step 1: Sync Your Local `dev` Branch with Upstream
Before starting any new branch, ensure your local code is up to date:

```bash
# 1. Switch to dev branch
git checkout dev

# 2. Pull the latest updates from the main HNG repository
git pull upstream dev

# 3. Push the fresh updates to Team Kestrel's fork
git push origin dev
```

---

### Step 2: Create Your Task 3 Feature Branch
The upstream repository strictly validates branch names using regex. Your branch name **MUST** start with `feat/task-3-`:

```bash
# Replace 'yourname' with your name or nickname
git checkout -b feat/task-3-yourname
```

* ✅ **Valid:** `feat/task-3-daniel`, `feat/task-3-update-name`
* ❌ **Invalid:** `task-3-daniel`, `feature/task3`, `daniel-task3` *(Rejected by CI bot!)*

---

### Step 3: Make Your Text Edit
Open the project in VS Code:
1. Open `src/app/(homepage)/contributors/zedu-kestrel/_lib/contributors.ts`.
2. Locate your entry and update your name/text (e.g., add your middle name or known name in parentheses).
3. Save the file.
4. Run `git diff` to make sure **only text** was changed and no styling or extra lines were touched:
   ```bash
   git diff
   ```

---

### Step 4: Run Local Verification Commands (Mandatory!)
Before committing, you **MUST** run all verification scripts. If any command reports an error, fix it before proceeding:

```bash
# 1. Format check
pnpm run check-format

# (If formatting fails, auto-fix it with: pnpm run format)

# 2. Lint check
pnpm run check-lint

# 3. TypeScript check (No errors allowed!)
pnpm run check-types

# 4. Production build test
pnpm run build
```

---

### Step 5: Commit Your Changes
We follow the **Conventional Commits** specification. The commit message must include your ticket ID (e.g. `KESTREL-001`) inside the parentheses:

```bash
git add .
git commit -m "feat(KESTREL-001): update yourname text for task 3"
```

* ✅ **Valid:** `feat(KESTREL-001): update daniel name for task 3`
* ❌ **Invalid:** `Feat: updated name`, `task 3 done`, `Update contributors.ts`

---

### Step 6: Sync & Push Your Branch to Team Kestrel's Fork (`origin`)

Before pushing, rebase with upstream in one line to ensure you have the freshest code and avoid merge conflicts:

```bash
git pull --rebase upstream dev
```

Now push your branch to GitHub:

```bash
git push -u origin feat/task-3-yourname
```

*(If you get a 403 error here, see [Git & GitHub Troubleshooting](tools-installation-guide/GIT_AND_GITHUB.md#q1-i-get-remote-permission-to-zedu-kestrelzedu-fegit-denied-to-username-fatal-unable-to-access--the-requested-url-returned-error-403))*

---

### Step 7: Open the Pull Request on GitHub
1. Open your browser and go to: **[https://github.com/zedu-hng/zedu-fe](https://github.com/zedu-hng/zedu-fe)**.
2. Click **Pull requests** ➔ **New pull request**.
3. Set the branches carefully:
   * **Base repository:** `zedu-hng/zedu-fe`
   * **Base branch:** `dev`
   * **Head repository:** `zedu-kestrel/zedu-fe`
   * **Compare branch:** `feat/task-3-yourname`
4. **PR Title (MUST include ticket ID):**  
   `feat(KESTREL-001): update yourname text for task 3`
5. **PR Description:** Copy and fill in this markdown template (matches upstream's `.github/pull_request_template.md`):

```markdown
## Ticket

- **Ticket ID:** KESTREL-004
- **Ticket title:** Update contributor bio text on Team Kestrel page

## Team lead

@yvnks

## What changed

Updated contributor text (name/bio/details) for Team Kestrel on the contributors page for Task 3 in `src/app/(homepage)/contributors/zedu-kestrel/_lib/contributors.ts`. No styling or layout changes made.

## Why

Task 3 requires every team member to make an atomic, non-breaking text update to verify the development workflow and CI/CD pipeline.

## How to test

1. Run `pnpm run check-format`
2. Run `pnpm run check-lint`
3. Run `pnpm run check-types`
4. Run `pnpm run build`
5. Visit `http://localhost:3000/contributors/zedu-kestrel` and verify your card text displays as expected.

## What to expect

The contributor card correctly reflects the updated text with zero styling, CSS, or layout changes.

## Backend

<!-- Leave empty: runs against dev backend -->

## Test evidence

- Tested against: local dev / fork build
- Tests: N/A, text-only static metadata update; all linters and build checks pass with 0 errors.

## Mandatory checks

- [x] **Atomic:** one logical change, at most ~400 lines of meaningful code (lockfiles and generated files like `*.tsbuildinfo` don't count). Larger needs a `size-override` label from a reviewer.
- [x] **Database / API contract:** schema changes follow Expand-Contract — no destructive drops or renames.
- [x] **Preview:** I checked the change in my fork's preview (or the fork build, if the team hasn't set up previews).
- [x] **Protected files:** I did not change `.github/`, `AGENTS.md`, `CONTRIBUTING.md` or tooling config without reviewer agreement and the `config-change-approved` label.

## Screenshots / recording

N/A, non-visual/text-only metadata change.

## AI usage

Assisted by AI assistant to format and validate Git branch and commit conventions.

## Checklist

- [x] Linked to an approved ticket
- [x] Only intended files changed
- [x] No secrets or debug code committed
- [x] Tests added/updated for what this ticket changed (not retroactive coverage of unrelated code)
- [x] Fork build triggered (first run: fork → Actions → PR build → Run workflow)
- [x] Team lead approved this PR
- [x] Self-reviewed (`git status` / `git diff`)
```

6. Click **Create pull request**.

---

### Step 8: Trigger the Fork Build Relay (CRITICAL!)
Because your branch is on the `zedu-kestrel` fork, the main `zedu-hng` CI needs your fork to execute the build test:

1. Open a new tab and visit your fork:  
   👉 **`https://github.com/zedu-kestrel/zedu-fe/actions`**
2. In the left sidebar, click **PR build**.
3. On the right side, click the **Run workflow** dropdown:
   * Branch: Select your branch (`feat/task-3-yourname`).
   * Click the green **Run workflow** button.
4. Wait 1–2 minutes until the run completes with a **green checkmark** ✅.
5. Go back to your open Pull Request on `zedu-hng/zedu-fe`.
6. Post a comment on your PR with this exact text:
   ```text
   /fork-build
   ```
7. The upstream bot will detect the successful run and mark your PR build as **Passed**!

---

### Step 9: Team Lead Review & Final Queue
* Tag our team leads **`@Fabito97`** or **`@yvnks`** in the PR comment or on the team chat for approval.
* Once approved, verify your PR is ready for merge by checking the official queue:  
  👉 **[Zedu Ready-for-Review Queue](https://github.com/zedu-hng/zedu-fe/pulls?q=is%3Apr+state%3Aopen+label%3Aready-for-review+status%3Asuccess)**
* Once claimed by HNG reviewers and merged into `dev`, Task 3 is complete! 🎉

---

## ❓ Frequently Asked Questions (Q&A)

### Q1: Can I change colors, fonts, or component styles?
**No!** Task 3 specifically requires a text-only update. Do not change colors, CSS classes, or styling rules.

### Q2: Can I delete another person's card in `contributors.ts`?
**No!** Only edit your own name/entry. Never delete or overwrite other team members' entries.

### Q3: What if someone else's PR was merged before I pushed?
Before pushing your branch, run:
```bash
git pull --rebase upstream dev
```
If there is a conflict in `contributors.ts`, simply keep both entries, run `pnpm run check-types`, and push.

### Q4: My PR is already open on GitHub and says "This branch is out of date with the base branch". What should I do?
1. **DO NOT click GitHub's "Update branch" button.** That button creates a `Merge branch 'dev'` commit that can trigger `commitlint` failures and violate HNG's single-author check.
2. Instead, update it cleanly from your terminal with these 3 commands:
- While on your feature branch eg feat/task-3-yourname
   ```bash
   git fetch upstream
   git rebase upstream/dev
   git push --force-with-lease origin feat/task-3-yourname
   ```

### Q5: What does `--force-with-lease` mean, and why is it needed?
* **Why it is needed:** When you rebase, Git rebuilds your commit on top of the latest code with a brand-new commit ID. Because the history changed, a standard `git push` is rejected.
* **What it means:** `--force-with-lease` is the **safe version of force-push**. It tells GitHub:
  > *"Update my branch with my newly rebased commit, BUT abort if someone else pushed changes to this branch that I haven't seen."*
* Unlike dangerous `git push --force` (which blindly wipes out remote work), `--force-with-lease` protects against accidental data loss while cleanly updating your pull request.
