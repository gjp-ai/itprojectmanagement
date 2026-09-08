---
description: Scan for secrets, push this repo to GitHub, set up GitHub Pages via Actions, and create/update the README and repo "About" section
argument-hint: [github-repo-url-or-owner/repo]
---

You are publishing this repository to GitHub end-to-end. The user may pass the target repo as `$ARGUMENTS` (a full URL like `https://github.com/owner/repo` or a shorthand like `owner/repo`). If it's missing, ask the user for it before doing anything destructive — do not guess or invent a repo name/owner.

Work through these steps **in order**. Stop and ask the user if anything is ambiguous or looks risky (e.g. the target repo already has unrelated history). Never force-push without explicit confirmation.

## 1. Scan for sensitive data BEFORE anything is pushed

This is a hard gate — do not proceed to step 2 until this passes.

- Run a broad scan of the working tree (not just staged files) for likely secrets: API keys, tokens, passwords, private keys, `.env` files, AWS/GCP/Azure credentials, connection strings, FormSubmit endpoints with real personal emails, hard-coded internal hostnames/IPs, etc. Use `git status`, `grep -rniE` for common secret patterns (e.g. `AKIA[0-9A-Z]{16}`, `-----BEGIN.*PRIVATE KEY-----`, `password\s*=`, `api[_-]?key`, `secret`, `token`), and check for any `.env*`, `*.pem`, `*.key`, credential JSON files, etc.
- Check `.gitignore` exists and covers common sensitive patterns (`.env`, `*.key`, `*.pem`, `node_modules`, etc.) — create/update it if missing.
- For this specific project (`index.html`), pay particular attention to the `FORMSUBMIT_ENDPOINT` value and any other embedded email addresses or identifiers — flag anything that looks like a real (non-demo) personal or corporate address, per CLAUDE.md's "no real UOB branding" / demo-only constraint.
- Report findings to the user in a short list. If anything sensitive is found, **do not push** — fix it (redact, add to `.gitignore`, remove from history if already committed) or get explicit user sign-off that it's intentional before continuing.

## 2. Upload the code to GitHub

- Confirm the target repo (from `$ARGUMENTS` or ask the user) and whether it already exists on GitHub or needs to be created (`gh repo create`).
- Check `git remote -v`. If no remote is set, add one pointing at the target repo.
- Verify current branch and status with `git status`/`git log` before pushing.
- Push the current branch (default: `main`) to the remote. Confirm with the user before any force-push or before pushing to a repo that already has unrelated commits.

## 3. Create/edit a GitHub Pages workflow

- Check if `.github/workflows/` already has a Pages deploy workflow (this repo currently has one at `.github/workflows/deploy.yml` or similar — check first, don't duplicate).
- If missing, add a minimal `actions/configure-pages` + `actions/upload-pages-artifact` + `actions/deploy-pages` workflow that deploys the repo root (this is a static single-file `index.html` app, no build step).
- If present, verify it's correct and up to date; edit only if broken or misconfigured.
- After pushing, remind the user they may need to enable Pages once in the repo settings (Settings → Pages → Source: GitHub Actions) if it's not already enabled — this cannot be done via git push alone, only via `gh api` or the web UI, so use `gh` if possible and otherwise tell the user the manual step.

## 4. Create/edit the README

- Check for an existing `README.md`. Write or update one that reflects what this project actually is (see CLAUDE.md: a vanilla single-file Kanban board demo for UOB IT PMO — no frameworks, no build step, no persistence).
- Include: what it is, how to run it (open `index.html` directly), key constraints/features, and a link to the live GitHub Pages URL once known.
- Keep it accurate to the actual code — don't invent features.

## 5. Capture a screenshot for the README

- Use the Playwright MCP tool to navigate to the live GitHub Pages URL (from step 3/4), resize the viewport to a reasonable desktop size (e.g. 1440x900), and take a screenshot.
- Save it into a `docs/` folder in the repo (e.g. `docs/screenshot.png`) rather than the repo root.
- Reference it near the top of `README.md` (e.g. right under the live demo link) with `![Screenshot of the Kanban board](docs/screenshot.png)`.
- Re-run this step on later publishes if the UI has changed meaningfully since the last screenshot.

## 6. Create/edit the GitHub repo "About" section

- Use `gh repo edit` to set the repo description and homepage URL (the GitHub Pages URL) on the "About" panel. This requires the Pages URL to be known — construct it from the org/user + repo name pattern (`https://<owner>.github.io/<repo>/`) or fetch it via `gh api repos/<owner>/<repo>/pages` after the first successful Pages deploy.
- Optionally set relevant topics (e.g. `demo`, `kanban`, `vanilla-js`) if the user wants them.

## 7. Wrap up

- Summarize what was done: remote pushed to, workflow status, README status, screenshot status, About/description/homepage set.
- Give the user the live Pages URL and note any manual step still required (e.g. first-time Pages enablement, FormSubmit activation email).
