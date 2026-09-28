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

## Everyday workflow

```bash
git status --short --branch
git pull --ff-only
git add <file-or-folder>
git diff --cached
git commit -m "Describe the change"
git push
```

Skip `git pull --ff-only` when there is no upstream yet or the remote is empty. On the very first push, use `git push --set-upstream origin HEAD` instead of `git push`.

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
