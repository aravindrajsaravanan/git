# Connect a Local Git Repository to GitHub

This guide walks through the everyday Git workflow: check your work, connect a local repository to GitHub, save changes as a commit, and push them to the remote repository.

> **This repository already has a remote named `origin`.** Its URL is `https://github.com/aravindrajsaravanan/git.git`. Check it with `git remote -v`; do not add another `origin`.

## Purpose

Git keeps a history of changes on your computer. GitHub hosts a copy of that repository online so you can back up your work and collaborate. A **remote** is the saved name and URL for that online repository; `origin` is the conventional name for the primary remote.

## Prerequisites

- Git installed (`git --version` should print a version).
- A local project folder. Open a terminal in that folder.
- A GitHub repository you can access. For a new local project, create an **empty** GitHub repository first (skip adding a README, license, or `.gitignore` there if the local project already has its own history).
- GitHub authentication through your browser, Git Credential Manager, or another trusted credential helper. Never put a password or token in a command or remote URL.

## 1. Check the local repository

Start by checking where you are and what has changed:

```bash
git status --short --branch
```

If this shows a branch and working-tree status, the current folder is already a Git repository. **Do not initialize it again.**

Only if Git reports that the folder is not a repository, move into the intended project folder and initialize it:

```bash
cd <path-to-your-project>
git init -b main
git status --short --branch
```

Replace `<path-to-your-project>` with your folder path. If your Git version does not support `git init -b`, run `git init` and then `git branch -M main`.

## 2. Check or configure the GitHub remote

List the configured remotes and their URLs:

```bash
git remote -v
```

### If a remote is already listed

For this repository, `origin` is already configured to the expected URL:

```text
https://github.com/aravindrajsaravanan/git.git
```

If `origin` points to the right repository, leave it as-is. If it points to a different repository, update it rather than adding a duplicate:

```bash
git remote set-url origin https://github.com/<OWNER>/<REPOSITORY>.git
```

Replace the placeholders with the owner and repository name. If `origin` is absent but the correct repository is listed under another name, rename that remote:

```bash
git remote rename <old-name> origin
```

### If no remote is configured

Add the GitHub repository as `origin`:

```bash
git remote add origin https://github.com/<OWNER>/<REPOSITORY>.git
```

Verify the result:

```bash
git remote -v
git remote get-url origin
```

Use the HTTPS URL shown on your GitHub repository page. GitHub will handle authentication through your credential helper or browser; do not embed credentials in the URL.

## 3. Get remote changes before you start (if applicable)

If this local branch already tracks a branch on GitHub, bring down its latest changes before editing:

```bash
git pull --ff-only
```

`--ff-only` updates your branch only when Git can do so without creating a merge commit. If it refuses, stop and inspect the local and remote histories instead of forcing the pull.

If you have uncommitted work, commit it or otherwise save it before pulling.

If the remote is empty, there is nothing to pull; make your first commit and push in the next steps.

### Caution: unrelated initial histories

If you made commits locally and also initialized the GitHub repository with its own README or other commit, the two repositories may have unrelated histories. A pull can then be refused. The simplest way to avoid this for a new project is to create an empty GitHub repository and push your existing local history to it. If both histories contain work you need, inspect them and make a backup before intentionally combining them. Only then consider:

```bash
git pull --no-rebase origin main --allow-unrelated-histories
```

Replace `main` with the remote branch name if needed, resolve any conflicts, review the result, and commit the merge. Do not use this option as a routine fix for a rejected push.

## 4. Stage and commit your changes

Review your working tree:

```bash
git status
```

Stage only the files you want included (replace the example path):

```bash
git add <file-or-folder>
```

To stage every change in the repository instead, use `git add -A` only after checking `git status`. Review exactly what is staged:

```bash
git diff --cached
```

Create a commit with a short description:

```bash
git commit -m "Describe the change"
```

A commit records a snapshot in your **local** history; it does not upload anything to GitHub.

## 5. Push to GitHub and set the upstream

For the first push of the current branch, set its upstream at the same time:

```bash
git push --set-upstream origin HEAD
```

`HEAD` means the branch you are currently on. `--set-upstream` (short form `-u`) links that local branch to its GitHub counterpart. After the first successful push, the usual command is:

```bash
git push
```

To set the upstream explicitly for a named branch instead, use:

```bash
git push --set-upstream origin <branch>
```

You can check the current branch and its tracking information with:

```bash
git branch -vv
```

If you forgot to set tracking and the remote branch already exists, connect the local branch to it:

```bash
git branch --set-upstream-to=origin/<branch> <branch>
git branch -vv
```

## Everyday workflow

The command list below covers the complete local-to-GitHub workflow described here. Run commands from the repository folder, one at a time, and read the notes before choosing an optional command.

### A. Start with an existing local project

Check Git and the current folder:

```bash
git --version
git status --short --branch
```

If Git says this folder is not a repository, initialize it; otherwise skip these commands:

```bash
git init -b main
git branch --show-current
```

If `git init -b main` is not supported, use `git init` followed by `git branch -M main`. Set the author name and email that should appear in your commits (these are not passwords or tokens):

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --get user.name
git config --get user.email
```

Use `--global` for all repositories on this computer. To set identity only for this repository, omit `--global`. If the repository already has commits and identity is configured, skip this setup.

Check whether a remote exists:

```bash
git remote -v
```

Then choose **one** remote action:

```bash
git remote add origin https://github.com/<OWNER>/<REPOSITORY>.git
```

Use this only when no remote named `origin` exists. If `origin` exists but points to the wrong repository, update it:

```bash
git remote set-url origin https://github.com/<OWNER>/<REPOSITORY>.git
```

If the right repository is listed under another remote name and `origin` is not in use, rename that remote:

```bash
git remote rename <old-name> origin
```

Verify before continuing:

```bash
git remote -v
git remote get-url origin
git branch -vv
```

For a new local project connected to an empty GitHub repository, stage, inspect, and create the first commit:

```bash
git status
git add <file-or-folder>
git diff --cached
git commit -m "Describe the change"
git push --set-upstream origin HEAD
```

Use `git add -A` instead of a specific path only when you intend to stage every change. The first push creates the remote branch and configures tracking; on later updates, use `git push`.

### B. Start from a repository that already exists on GitHub

Clone it once to create a local copy (replace the placeholders with the repository URL and a destination folder):

```bash
git clone https://github.com/<OWNER>/<REPOSITORY>.git <folder-name>
cd <folder-name>
git status --short --branch
git remote -v
```

`git clone` configures `origin` automatically. Do not run `git remote add origin` in the cloned repository.

### C. Repeat the daily edit-and-upload cycle

Check the branch and working tree, get compatible incoming changes if the branch already tracks GitHub, then stage and review the files you intend to save:

```bash
git status --short --branch
git pull --ff-only
git add <file-or-folder>
git diff
git diff --cached
git status
git commit -m "Describe the change"
git push
```

Skip `git pull --ff-only` on an empty remote or when there is no upstream branch yet. Use `git push --set-upstream origin HEAD` for the first push instead of `git push`. `git diff` shows unstaged changes; `git diff --cached` shows staged changes.

### D. Useful inspection and recovery commands

These commands help you inspect history and handle common situations:

```bash
git log --oneline --decorate --graph -10
git branch
git branch -a
git branch -vv
git fetch origin
git status
git diff
git diff --cached
git restore --staged <file>
git restore <file>
git revert <commit>
```

- `git log` displays recent commits; change `-10` to show a different number.
- `git branch` lists local branches; `git branch -a` includes remote-tracking branches.
- `git fetch origin` downloads remote updates without merging them into your current branch.
- `git restore --staged <file>` unstages a file but keeps its edits.
- **Caution:** `git restore <file>` discards that file's unstaged edits. `git revert <commit>` makes a new commit that reverses an earlier commit; replace `<commit>` with a commit ID from `git log`.

To create and move between local branches:

```bash
git switch -c <new-branch>
git switch <branch>
git branch -m <new-name>
git branch -d <branch>
```

`git switch -c` creates and checks out a branch; `git switch` moves to an existing branch. `git branch -m` renames the current branch. `git branch -d` deletes a local branch only after Git considers it merged; do not delete a branch with work you still need.

When a remote branch has commits you need, fetch and inspect before integrating. For a local branch that is already related to the named remote branch, you can explicitly pull it with fast-forward-only behavior:

```bash
git fetch origin
git branch -r
git pull --ff-only origin <branch>
```

To push a named local branch instead of the current branch:

```bash
git push --set-upstream origin <branch>
```

For an intentional merge of separate initial histories only, first inspect and back up both histories, then use the command in the unrelated-histories caution above. Resolve conflicts, run `git status`, stage the resolved files with `git add <file-or-folder>`, inspect with `git diff --cached`, and finish with `git commit -m "Merge GitHub and local histories"`. Do not use `git push --force` to get around a rejected push.

### Short daily reminder

```bash
git status --short --branch
git pull --ff-only
git add <file-or-folder>
git diff --cached
git commit -m "Describe the change"
git push
```

The short reminder is for an already-connected branch. Skip `git pull --ff-only` when there is no upstream yet or the remote is empty. On the very first push, use `git push --set-upstream origin HEAD` instead of `git push`.

**Workflow diagram (not a screenshot):**

```mermaid
flowchart LR
    A[Local files] -->|git add| B[Staging area]
    B -->|git commit| C[Local history]
    C -->|git push| D[GitHub remote]
    D -->|git pull| C
```

## Troubleshooting

- **`remote origin already exists`:** Run `git remote -v`. Keep it if correct; otherwise use `git remote set-url origin <URL>`. Do not run `git remote add origin` again.
- **`no upstream branch`:** Make the first push with `git push --set-upstream origin HEAD`. Later pushes can use `git push`.
- **`src refspec ... does not match any`:** Check `git branch --show-current` and `git status`. Make sure you are on the intended branch and have at least one commit before pushing.
- **Push rejected as non-fast-forward:** The remote has commits you do not have. Fetch and inspect the histories before integrating them. If the histories are related and your branch tracks the remote, `git pull --ff-only` is a safe first attempt; do not force-push to bypass the rejection.
- **Merge conflict:** Run `git status`, open each conflicted file, resolve the conflict markers, then stage the resolved files and commit. Review the final diff before pushing.
- **Authentication failed:** Sign in through your Git credential manager or browser, or use an approved credential helper. Never paste an access token into a command, commit, or remote URL.
