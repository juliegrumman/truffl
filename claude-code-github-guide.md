# Connecting Claude Code Desktop App to GitHub & Deploying to Production

A quick-start guide for clients.

---

## 1. Prerequisites

- **Claude Code Desktop App** installed ([download here](https://claude.ai/download))
- **GitHub account** with access to your repository
- **Git** installed locally (`git --version` to check)
- **GitHub CLI (`gh`)** — not included with Claude Code, must be installed separately (see below)

### Install GitHub CLI

| Platform | Command |
|----------|---------|
| macOS | `brew install gh` |
| Windows | `winget install --id GitHub.cli` |
| Linux (Debian/Ubuntu) | See [cli.github.com/manual/installation](https://cli.github.com/manual/installation) |

Verify: `gh --version`

---

## 2. Authenticate GitHub CLI

Run in your terminal:

```bash
gh auth login
```

Follow the prompts:
1. Select **GitHub.com** (or GitHub Enterprise if applicable)
2. Choose your preferred auth method — **Login with a web browser** is easiest
3. Complete the browser-based OAuth flow
4. When prompted for default git protocol, choose **HTTPS** (simplest) or **SSH**

Verify it worked:

```bash
gh auth status
```

You should see `Logged in to github.com as <your-username>`.

---

## 3. Clone Your Repository

```bash
gh repo clone owner/repo-name
cd repo-name
```

Or if you already have it cloned, just `cd` into the directory.

---

## 4. Open the Project in Claude Code Desktop

1. Open the **Claude Code desktop app**
2. Use **File > Open Folder** (or drag the folder in) to open your cloned repo
3. Claude Code now has full context of your codebase

---

## 5. Make Changes with Claude Code

Ask Claude to make changes in natural language, e.g.:

- *"Fix the null pointer error in `src/api/handler.js`"*
- *"Add input validation to the signup form"*
- *"Refactor the database queries to use connection pooling"*

Claude will edit files directly. Review the diffs before proceeding.

---

## 6. Commit Your Changes

Option A — **Ask Claude directly:**

> "Commit these changes"

Claude will stage the relevant files and create a commit with an appropriate message.

Option B — **Do it manually via terminal:**

```bash
git add src/api/handler.js        # stage specific files
git commit -m "Fix null pointer in handler"
```

---

## 7. Push to a Branch & Open a Pull Request

### Create a branch, push, and open a PR in one flow:

```bash
# Create and switch to a new branch
git checkout -b fix/null-pointer-handler

# Push to remote
git push -u origin fix/null-pointer-handler

# Open a pull request
gh pr create --title "Fix null pointer in handler" --body "Resolves the crash on empty input"
```

Or ask Claude:

> "Create a PR for these changes"

### Check PR status:

```bash
gh pr status
gh pr checks          # view CI/CD check results
```

---

## 8. Merge to Production

Once the PR is approved and checks pass:

```bash
# Merge the PR (uses your repo's default merge strategy)
gh pr merge --auto

# Or specify a strategy:
gh pr merge --squash      # squash and merge
gh pr merge --merge       # merge commit
gh pr merge --rebase      # rebase and merge
```

### If your repo deploys on merge to `main`:

The merge itself triggers production deployment. Monitor it:

```bash
# List recent workflow runs
gh run list --limit 5

# Watch a specific run in real time
gh run watch <run-id>

# View details if something fails
gh run view <run-id> --log-failed
```

---

## 9. Common Workflows at a Glance

| Task | Command |
|------|---------|
| Check repo status | `gh repo view` |
| List open PRs | `gh pr list` |
| Review a PR | `gh pr diff <number>` |
| Approve a PR | `gh pr review <number> --approve` |
| Check deploy status | `gh run list` |
| View failed logs | `gh run view <id> --log-failed` |
| Create a release | `gh release create v1.0.0 --generate-notes` |

---

## 10. Tips

- **Always work on a branch** — never commit directly to `main`/`production`
- **Review diffs before committing** — Claude edits files directly, so verify changes
- **Use `gh pr checks`** to ensure CI passes before merging
- **Use `--auto` merge** to auto-merge once all required checks pass
- **Ask Claude for help** — you can ask it to explain PR comments, debug failing CI, or draft release notes right from the desktop app
