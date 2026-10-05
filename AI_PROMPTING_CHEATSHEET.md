# AI Prompting Cheatsheet for Team Kestrel

Many contributors on **Team Kestrel** use AI coding assistants (such as **Antigravity**, **Cursor**, **Claude Code**, or **GitHub Copilot**).

Because the upstream repository (`zedu-hng/zedu-fe`) enforces strict automated CI/CD bots and linters, an unguided AI model will easily violate rules (e.g. inserting hardcoded `http(s)://` URLs, missing JSDoc comments, modifying `.github/` workflows, or formatting commit messages incorrectly).

Use the copy-paste templates below to constrain your AI assistant and guarantee first-time passing builds!

---

## 1. System Prompt / Kickoff Prompt for Any AI Coding Assistant

Copy and paste this block into your AI agent at the very beginning of your task:

```markdown
I am working on the Zedu Frontend repository (Next.js 15, TypeScript, Tailwind CSS, pnpm) as part of Team Kestrel for HNG 15.

Here are the strict engineering guidelines you MUST adhere to:
1. NEVER add hardcoded 'http://' or 'https://' URLs in JSX, TSX, TS, CSS, or JSON files. The upstream PR CI bot checks for this and will immediately fail. If external links are needed, use allowlisted hosts (e.g., avatars.githubusercontent.com) or use relative links / mailto: schemes.
2. NEVER modify or create files inside `.github/` (including workflows, PR review scripts, or bot configurations).
3. Do NOT edit existing code outside our assigned feature scope.
4. Add comprehensive JSDoc docstrings to ALL exported components, functions, interfaces, and helpers to satisfy CodeRabbit docstring coverage rules (>80%).
5. Strict TypeScript only: NO `any`, NO `@ts-ignore`, NO unused variables or imports.
6. Run and verify all local checks before finalizing:
   - `pnpm run check-format`
   - `pnpm run check-lint`
   - `pnpm run check-types`
   - `pnpm run build`
7. Branch naming convention: `feat/<task-number>-<short-description>` or `fix/<task-number>-<short-description>`.
8. Commit message convention: lowercase conventional commit format with ticket ID (e.g., `feat(KESTREL-001): add short description`).
```

---

## 2. Feature Implementation Prompt Template

When you receive a task or issue (e.g. from Jira, Linear, or GitHub issues), use this template:

```markdown
I need to implement the following task on our branch:
Task ID: <TASK_ID>
Task Title: <TASK_TITLE>
Requirements:
- <Describe requirements or paste from task card>

Please follow these steps:
1. Ensure we are branched off the latest `dev` branch with name: `feat/<task-id>-<slug>`.
2. Implement the component / page using Next.js App Router conventions and Tailwind CSS.
3. Keep all components clean and modular with proper JSDoc docstrings for every function and prop.
4. Ensure no external `https://` URLs are hardcoded in the codebase.
5. Run `pnpm run check-types` and `pnpm run build` and resolve any errors before presenting the finished code.
```

---

## 3. Pre-PR Verification & Git Commit Prompt

When the AI has finished coding and you are ready to commit:

```markdown
We are ready to prepare our changes for submission. Please:
1. Check `git status` to verify ONLY the intended files were created or modified. Make sure no `.github/` or unnecessary config files were touched.
2. Run the full verification suite:
   - `pnpm run check-format`
   - `pnpm run check-lint`
   - `pnpm run check-types`
   - `pnpm run build`
3. If formatting fails, run `pnpm run format` to fix styling issues.
4. Provide a conventional commit message in lowercase format matching:
   `<type>(<TICKET-ID>): <short imperative description>`
   (e.g., `feat(KESTREL-001): add team kestrel page`)
```

---

## 4. PR Description Generator Prompt

When you are ready to open a Pull Request on GitHub:

```markdown
Please generate a GitHub Pull Request description using our mandatory PR template for this change:
- PR Title (must include ticket ID in parenthesis, e.g. `feat(KESTREL-001): add team contributors page`)
- Summary of changes
- Motivation and context
- Testing done (confirming format, lint, types, build passed)
- Verification checklist (no hardcoded URLs, JSDoc docstrings added, etc.)

Format it in clean GitHub markdown so I can copy and paste it directly into the GitHub PR body.
```

---

## 5. Common AI Failure Traps & How to Prevent Them

| AI Tendency | Upstream CI Failure | Solution / Correction Prompt |
| :--- | :--- | :--- |
| Inserts social links (`https://linkedin.com/...`, `https://twitter.com/...`) | **Structure, reuse, URLs check fails** (`regex /https?:\/\//` match) | "Remove all external `https://` URLs. Use `mailto:` or allowlisted domains only." |
| Writes concise functions without JSDoc comments | **CodeRabbit PR Review fails docstring ratio check** | "Add standard JSDoc comments explaining parameters, return values, and component purpose." |
| Capitalizes PR title (`Feat: Add contributor page`) | **Commitlint PR Title bot check fails** | "Make the PR title strictly lowercase: `feat(scope): add contributor page`." |
| Tries to modify `.github/workflows` to fix CI errors | **Security / Branch protection rejection** | "Do not modify any files in `.github/`. Fix the underlying source code." |
| Uses `any` type for complex objects | **`pnpm run check-types` fails** | "Define explicit TypeScript interfaces for all data structures." |
