---
name: git-manager
description: Complete git workflow management — status, add, commit, push, pull, fetch, log, diff, branch. Use when the user wants to check repo status, commit changes, push to remote, or perform any git operation.
argument-hint: "<command> [args]"
---

# Git Manager — 完整 Git 工作流管理

Manage git repositories: check status, stage files, commit, push, pull, view history, and handle branches.

## Arguments

$ARGUMENTS contains the git command and any arguments, e.g.:
- `status` — show working tree status
- `add <file>` — stage specific files
- `commit -m "message"` — commit staged changes
- `push` — push to remote
- `pull` — pull from remote
- `fetch` — fetch from remote
- `log [--oneline -10]` — view commit history
- `diff [file]` — show unstaged changes
- `branch` — list branches
- `checkout <branch>` — switch branch
- `sync` — commit all + push (full sync)

If no command is given, show a summary of the current git state.

## Workflow

### 1. Parse the command

Determine which git operation the user wants:
- No args → show status summary
- Known command → execute it
- Unknown → show available commands

### 2. Execute git operations

**status** — Show current working tree state:
```bash
git status
```

**add** — Stage files (if no specific file given, show what needs to be added):
```bash
git add <file1> <file2>
```

**commit** — Create a commit (prompt for message if not provided):
```bash
git commit -m "<message>"
```

**push** — Push to remote:
```bash
git push
```
If HTTPS push fails with network errors, try fallback via GitHub API:
1. Check if `api.github.com` is reachable
2. Extract owner/repo from remote URL
3. Use GitHub Contents API to push each changed file

**pull** — Pull from remote:
```bash
git pull
```

**fetch** — Fetch from remote:
```bash
git fetch
```

**log** — Show commit history (default: last 10 commits, oneline):
```bash
git log --oneline -10
```

**diff** — Show unstaged changes:
```bash
git diff
```

**branch** — List branches:
```bash
git branch -a
```

**sync** — Full sync workflow:
1. `git add -A`
2. `git commit -m "<message>"`
3. `git push`

### 3. Report results

Present the output clearly, highlighting:
- Success/failure status
- Number of files changed/added
- Commit hashes (short)
- Any errors that need user action

## Notes

- For commits, always use `git commit -m` with a heredoc to handle multi-line messages
- Prefer adding specific files over `git add -A` to avoid committing unintended files
- If `git push` fails due to network (GitHub port 443 blocked), attempt GitHub API fallback
- The API fallback uses `https://api.github.com/repos/{owner}/{repo}/contents/{path}` for each changed file
- Never force push unless the user explicitly requests it
- Never skip hooks (--no-verify) unless the user explicitly requests it
