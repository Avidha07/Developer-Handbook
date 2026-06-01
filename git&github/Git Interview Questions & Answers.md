# 🧠 Git Interview Questions & Answers

> A comprehensive guide to Git concepts explained in a clear, interview-ready style.

---

## Table of Contents

1. [What is Git?](#1-what-is-git)
2. [Difference between Git and GitHub](#2-difference-between-git-and-github)
3. [What is a Repository?](#3-what-is-a-repository)
4. [git pull vs git fetch](#4-git-pull-vs-git-fetch)
5. [git merge vs git rebase](#5-git-merge-vs-git-rebase)
6. [What is a Branch in Git?](#6-what-is-a-branch-in-git)
7. [Why do we use Pull Requests?](#7-why-do-we-use-pull-requests)
8. [What is Git Stash?](#8-what-is-git-stash)
9. [git reset vs git revert](#9-git-reset-vs-git-revert)
10. [What is a Merge Conflict?](#10-what-is-a-merge-conflict)
11. [How do you resolve merge conflicts?](#11-how-do-you-resolve-merge-conflicts)
12. [Local Repository vs Remote Repository](#12-local-repository-vs-remote-repository)
13. [What is .gitignore?](#13-what-is-gitignore)
14. [git clone vs git fork](#14-git-clone-vs-git-fork)
15. [What is HEAD in Git?](#15-what-is-head-in-git)
16. [git add . vs git add \<file\>](#16-git-add--vs-git-add-file)
17. [What is Detached HEAD state?](#17-what-is-detached-head-state)
18. [Git Workflow in Teams](#18-git-workflow-in-teams)
19. [How do you undo a commit?](#19-how-do-you-undo-a-commit)
20. [Debugging: Code works locally but fails after merge](#20-debugging-code-works-locally-but-fails-after-merge)

---

## 1. What is Git?

**Git is a distributed version control system** that tracks changes in source code during software development.

Think of Git as a **time machine for your code**. Every time you make a meaningful change, Git takes a snapshot of your project. You can go back to any previous snapshot, see who changed what and when, and collaborate with others without overwriting each other's work.

**Key characteristics:**
- **Distributed** — every developer has a full copy of the repository, including its entire history
- **Fast** — most operations happen locally without needing a network
- **Free & Open Source** — created by Linus Torvalds in 2005

```bash
# Check if Git is installed
git --version

# Configure Git for the first time
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

---

## 2. Difference between Git and GitHub

| Feature | Git | GitHub |
|--------|-----|--------|
| **What it is** | A version control tool | A cloud-based hosting platform for Git repositories |
| **Where it runs** | Locally on your machine | On the web (remote server) |
| **Purpose** | Tracks code changes | Enables collaboration, code review, CI/CD |
| **Owned by** | Open-source (community) | Microsoft |
| **Alternatives** | — | GitLab, Bitbucket, Azure DevOps |

> **In simple terms:** Git is the engine. GitHub is the garage where you park and share your projects.

---

## 3. What is a Repository?

A **repository (repo)** is a storage location where your project files and their entire revision history are kept.

There are two types:

- **Local Repository** — lives on your own computer (`.git` folder inside your project)
- **Remote Repository** — hosted on a server (e.g., GitHub, GitLab)

```bash
# Initialize a new local repository
git init my-project

# OR clone an existing remote repository
git clone https://github.com/user/repo.git
```

---

## 4. git pull vs git fetch

This is a **very common interview question**. Both commands download changes from a remote, but they behave differently.

| | `git fetch` | `git pull` |
|--|------------|-----------|
| **What it does** | Downloads remote changes but does NOT merge them | Downloads AND automatically merges into your current branch |
| **Safe?** | ✅ Safe — your working directory is untouched | ⚠️ Can cause merge conflicts if local work exists |
| **Use when** | You want to review changes before merging | You trust the remote and want to update quickly |

```bash
# Fetch only — inspect before merging
git fetch origin
git diff main origin/main   # see what changed

# Pull — fetch + merge in one step
git pull origin main
```

> **Pro tip:** `git pull` = `git fetch` + `git merge`. When in doubt, use `fetch` first.

---

## 5. git merge vs git rebase

Both integrate changes from one branch into another, but they do it differently.

### `git merge`
Creates a **new merge commit** that joins two branch histories. The original history of both branches is preserved.

```
main:    A---B---C---M
                    /
feature:       D---E
```

### `git rebase`
**Replays** commits from your feature branch on top of the target branch. Results in a clean, linear history.

```
main:    A---B---C
feature:          D'--E'   (commits replayed on top of C)
```

| | `git merge` | `git rebase` |
|--|-------------|--------------|
| **History** | Preserves full history with merge commit | Creates a clean, linear history |
| **Use for** | Public/shared branches | Local/feature branches before PR |
| **Risk** | None — non-destructive | Rewrites history — don't rebase shared branches |

```bash
# Merge feature into main
git checkout main
git merge feature-branch

# Rebase feature onto main
git checkout feature-branch
git rebase main
```

> **Golden Rule:** Never rebase commits that have been pushed to a shared remote branch.

---

## 6. What is a Branch in Git?

A **branch** is an independent line of development. It allows you to work on a new feature or bug fix in isolation without affecting the main codebase.

By default, Git starts you on the `main` (or `master`) branch. When you create a new branch, you're creating a lightweight, moveable pointer to a specific commit.

```bash
# Create and switch to a new branch
git checkout -b feature/login-page

# List all branches
git branch -a

# Switch between branches
git checkout main

# Delete a branch after merging
git branch -d feature/login-page
```

**Common branching strategies:**
- `main` / `master` — production-ready code
- `develop` — integration branch
- `feature/*` — new features
- `hotfix/*` — urgent production fixes
- `release/*` — release preparation

---

## 7. Why do we use Pull Requests?

A **Pull Request (PR)** — called a **Merge Request (MR)** in GitLab — is a formal way to propose merging code from one branch into another.

**Why we use them:**
1. **Code Review** — teammates can review your code, leave comments, and suggest improvements before it's merged
2. **Quality Gate** — you can require approvals, run automated tests (CI), and enforce checks
3. **Discussion** — keeps conversation about a feature or fix linked directly to the code
4. **Audit Trail** — every change has a documented reason and reviewer

**Typical PR workflow:**
```
1. Developer creates feature branch
2. Pushes branch to remote
3. Opens a Pull Request with a description
4. Reviewers leave comments / request changes
5. Developer makes fixes and pushes again
6. PR is approved and merged into main
```

---

## 8. What is Git Stash?

`git stash` **temporarily shelves** (stashes) changes you've made to your working directory so you can work on something else and come back to those changes later.

**Real-world scenario:** You're in the middle of a feature, your manager asks for an urgent hotfix on `main`. You don't want to commit half-done work — so you stash it.

```bash
# Stash current changes
git stash

# List all stashes
git stash list

# Apply the most recent stash (keeps it in the stash list)
git stash apply

# Apply and remove the most recent stash
git stash pop

# Apply a specific stash
git stash apply stash@{2}

# Drop (delete) a stash
git stash drop stash@{0}

# Clear all stashes
git stash clear
```

> Stash also saves untracked files with: `git stash -u`

---

## 9. git reset vs git revert

Both undo changes, but they work very differently and are used in different situations.

### `git reset`
Moves the branch pointer backward, **erasing commits** from history. This is destructive.

```bash
# Unstage files (keep changes in working directory)
git reset HEAD~1            # soft — keeps changes staged
git reset --mixed HEAD~1    # default — unstages changes
git reset --hard HEAD~1     # DANGER — deletes changes permanently
```

### `git revert`
Creates a **new commit** that undoes the changes of a previous commit. History is preserved.

```bash
# Safely undo a specific commit
git revert <commit-hash>
```

| | `git reset` | `git revert` |
|--|-------------|--------------|
| **How it works** | Moves HEAD backward | Creates a new "undo" commit |
| **History** | Rewrites / erases history | Preserves history |
| **Safe for shared branches?** | ❌ No | ✅ Yes |
| **Use when** | Local commits, not yet pushed | Already pushed / shared commits |

> **Interview answer:** Use `revert` on public branches, use `reset` only on local/private commits.

---

## 10. What is a Merge Conflict?

A **merge conflict** occurs when two branches have made changes to the **same line of the same file**, and Git cannot automatically determine which version to keep.

Git will pause the merge and ask you to resolve it manually.

**Common causes:**
- Two developers edited the same line in a file
- One developer deleted a file another developer modified
- Parallel changes to the same function

Git marks conflicts in the file like this:
```
<<<<<<< HEAD
  console.log("Hello from main branch");
=======
  console.log("Hello from feature branch");
>>>>>>> feature/greeting
```

---

## 11. How do you resolve merge conflicts?

**Step-by-step resolution:**

```bash
# Step 1: Attempt the merge
git merge feature-branch
# → CONFLICT message appears

# Step 2: See which files have conflicts
git status

# Step 3: Open the conflicting file and look for conflict markers
# <<<<<<< HEAD
#   your changes
# =======
#   incoming changes
# >>>>>>> feature-branch

# Step 4: Edit the file — keep what's correct, remove the markers

# Step 5: Stage the resolved file
git add src/app.js

# Step 6: Complete the merge
git commit -m "Resolve merge conflict in app.js"
```

**Tools to help:**
```bash
git mergetool         # Opens a visual diff/merge tool
code --wait file.js   # VS Code has excellent built-in conflict resolution UI
```

> **Best practice:** Communicate with teammates, keep branches short-lived, and pull from main frequently to minimize conflicts.

---

## 12. Local Repository vs Remote Repository

| | Local Repository | Remote Repository |
|--|-----------------|-------------------|
| **Location** | On your computer | On a server (GitHub, GitLab, etc.) |
| **Access** | Only you (offline) | Everyone with permissions |
| **Purpose** | Day-to-day development | Collaboration, backup, CI/CD |
| **Speed** | Very fast (no network) | Depends on network |

```bash
# View remote connections
git remote -v

# Add a remote
git remote add origin https://github.com/user/repo.git

# Push local changes to remote
git push origin main

# Pull remote changes to local
git pull origin main
```

---

## 13. What is .gitignore?

A `.gitignore` file tells Git which files and folders to **intentionally ignore** and not track.

**Why it's important:**
- Keeps sensitive information (API keys, passwords) out of version control
- Avoids committing build artifacts, dependencies, and OS-specific files

```bash
# Example .gitignore file

# Dependencies
node_modules/
vendor/

# Build output
dist/
build/
*.class

# Environment variables
.env
.env.local

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*

# IDE settings
.vscode/
.idea/
```

```bash
# If a file is already tracked, untrack it:
git rm --cached filename
echo "filename" >> .gitignore
git commit -m "Stop tracking filename"
```

> GitHub provides a collection of useful `.gitignore` templates at [github.com/github/gitignore](https://github.com/github/gitignore)

---

## 14. git clone vs git fork

| | `git clone` | Fork |
|--|-------------|------|
| **What it does** | Copies a repo to your local machine | Copies a repo to your own GitHub account |
| **Where it lives** | Your local computer | Your GitHub/GitLab account (remote) |
| **Connection to original** | Linked via `origin` remote | Completely independent copy |
| **Use case** | Working on your own or team repos | Contributing to open-source projects |

```bash
# Clone — creates a local copy
git clone https://github.com/original-owner/repo.git

# After forking on GitHub, clone YOUR fork
git clone https://github.com/YOUR-USERNAME/repo.git

# Add the original repo as "upstream" to stay in sync
git remote add upstream https://github.com/original-owner/repo.git
git fetch upstream
git merge upstream/main
```

> **Workflow:** Fork → Clone → Branch → Code → PR to original repo

---

## 15. What is HEAD in Git?

**HEAD is a pointer** that refers to the currently checked-out commit or branch.

Think of HEAD as "where you are right now" in the repository's history.

```bash
# HEAD points to a branch (normal state)
HEAD → main → commit abc123

# See what HEAD points to
cat .git/HEAD
# output: ref: refs/heads/main

# See the commit HEAD is on
git log --oneline -1
```

**HEAD~1** means "one commit before HEAD", useful for reset/revert:
```bash
git reset HEAD~1     # go back one commit
git diff HEAD~3      # compare to 3 commits ago
```

---

## 16. git add . vs git add \<file\>

| Command | What it does |
|---------|-------------|
| `git add .` | Stages **all** changes in the current directory and subdirectories |
| `git add <file>` | Stages only the specified file |
| `git add *.js` | Stages all `.js` files |
| `git add src/` | Stages all changes inside the `src/` folder |

```bash
# Stage everything (use with caution)
git add .

# Stage specific files
git add index.html styles.css

# Stage parts of a file interactively (patch mode)
git add -p app.js

# Check what's staged before committing
git status
git diff --staged
```

> **Best practice:** Use `git add <file>` for meaningful, focused commits. Avoid `git add .` unless you've reviewed all changes with `git status` first.

---

## 17. What is Detached HEAD State?

A **detached HEAD state** means HEAD is pointing directly to a **commit** instead of a branch. You're no longer "on" any branch.

This happens when you:
- Checkout a specific commit hash: `git checkout abc1234`
- Checkout a tag: `git checkout v1.0.0`

```bash
# This puts you in detached HEAD state
git checkout abc1234

# Warning you'll see:
# HEAD is now at abc1234...
# You are in 'detached HEAD' state.
```

**The risk:** Any commits you make in this state are "orphaned" — they don't belong to any branch and can be lost by garbage collection.

**How to recover:**
```bash
# Create a branch from detached HEAD to save your work
git checkout -b my-recovery-branch

# Or just go back to a real branch
git checkout main
```

---

## 18. Git Workflow in Teams

A typical team Git workflow (based on **GitHub Flow** or **Git Flow**):

```
1. Pull latest changes from main
   git pull origin main

2. Create a feature branch
   git checkout -b feature/user-authentication

3. Make commits as you work
   git add .
   git commit -m "Add login form validation"

4. Push the branch to remote
   git push origin feature/user-authentication

5. Open a Pull Request on GitHub

6. Code review → address feedback → push more commits

7. CI/CD pipeline runs automated tests

8. PR approved → merge into main

9. Delete the feature branch
   git branch -d feature/user-authentication

10. Deploy from main
```

**Key principles:**
- `main` is always deployable
- Never commit directly to `main`
- Branches are short-lived (days, not weeks)
- Communicate early with PRs (draft PRs are great for this)

---

## 19. How do you undo a commit?

There are several ways depending on your situation:

### Scenario 1: Undo last commit, keep changes (not yet pushed)
```bash
git reset --soft HEAD~1    # changes remain staged
git reset --mixed HEAD~1   # changes remain in working directory (default)
```

### Scenario 2: Undo last commit, discard changes (not yet pushed)
```bash
git reset --hard HEAD~1    # ⚠️ permanently deletes changes
```

### Scenario 3: Already pushed to remote (safe undo)
```bash
git revert HEAD            # creates a new "undo" commit
git push origin main
```

### Scenario 4: Fix the last commit message or add a file
```bash
git add forgotten-file.js
git commit --amend -m "Corrected commit message"
# Then force push if already pushed (only on your own branch)
git push --force-with-lease
```

> **Rule of thumb:** If the commit is only local → `reset`. If it's pushed to a shared branch → `revert`.

---

## 20. Debugging: Code works locally but fails after merge

This is a **real-world senior-level question**. Here's how to approach it systematically:

### Step 1: Understand what changed
```bash
# See all commits that came in with the merge
git log main..HEAD --oneline

# See all file changes introduced by the merge
git diff HEAD~1 HEAD
```

### Step 2: Identify the conflicting commit
```bash
# Use bisect to binary-search for the bad commit
git bisect start
git bisect bad                  # current state is broken
git bisect good <last-good-sha> # known good commit
# Git checks out commits for you to test
# Mark each as good or bad until the culprit is found
git bisect good   # or: git bisect bad
git bisect reset  # when done
```

### Step 3: Compare environments
```bash
# Check if environment variables or configs differ
cat .env         # local
cat .env.example # check if new keys were added by someone else

# Check if dependencies changed
git diff HEAD~1 HEAD -- package.json
npm install      # re-install to sync
```

### Step 4: Check for silent merge issues
```bash
# View the actual merge commit's diff
git show <merge-commit-hash>

# Look for accidentally overwritten code
git log -p --follow src/affected-file.js
```

### Common root causes:
| Cause | Solution |
|-------|----------|
| New environment variable added | Sync `.env.example` → update `.env` |
| Dependency version conflict | Delete `node_modules`, run `npm ci` |
| Code from both branches kept incorrectly | Review merge commit diff carefully |
| Database migration missing | Run pending migrations |
| Merge resolved a conflict incorrectly | Use `git show` to inspect the merge commit |

---

## Quick Reference Cheatsheet

```bash
# ── Setup ──────────────────────────────────────
git init                        # Initialize repo
git clone <url>                 # Clone remote repo
git config --global user.name "Name"

# ── Staging & Committing ───────────────────────
git status                      # Check working tree
git add <file>                  # Stage a file
git add .                       # Stage all changes
git commit -m "message"         # Commit staged changes
git commit --amend              # Modify last commit

# ── Branching ─────────────────────────────────
git branch                      # List branches
git checkout -b <branch>        # Create & switch branch
git merge <branch>              # Merge branch into current
git rebase <branch>             # Rebase onto branch
git branch -d <branch>          # Delete branch

# ── Remote ────────────────────────────────────
git remote -v                   # View remotes
git fetch origin                # Fetch without merging
git pull origin main            # Fetch + merge
git push origin <branch>        # Push branch to remote

# ── Undoing ────────────────────────────────────
git reset --soft HEAD~1         # Undo commit, keep staged
git reset --hard HEAD~1         # Undo commit, discard changes
git revert <hash>               # Safe undo (new commit)
git stash                       # Shelve current changes
git stash pop                   # Restore stashed changes

# ── Inspection ─────────────────────────────────
git log --oneline --graph       # Visual commit history
git diff                        # Unstaged changes
git diff --staged               # Staged changes
git show <hash>                 # Show a specific commit
git blame <file>                # Who changed each line
git bisect start                # Binary search for bug
```

---

## Resources

- 📖 [Official Git Documentation](https://git-scm.com/doc)
- 🎮 [Learn Git Branching (Interactive)](https://learngitbranching.js.org/)
- 📋 [GitHub Docs](https://docs.github.com)
- 🗂️ [gitignore Templates](https://github.com/github/gitignore)
- 📘 [Pro Git Book (Free)](https://git-scm.com/book/en/v2)

---

*Made with ❤️ for interview preparation. Good luck! 🚀*
