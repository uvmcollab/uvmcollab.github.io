---
icon: material/git
---

# Git Basics

A quick reference to configure Git, clone repositories, and manage branches efficiently.

## 1. Initial configurations

Ensure that your Git version is **1.8 or newer**:

```bash
git --version
```

### Global configurations

Before using Git for the first time, set your global username and email (these will be attached to all your commits):

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Check your configuration at any time:

```bash
git config --list
```

### Local configurations

If you contribute to multiple repositories or use different accounts, you can override the global configuration for a specific repository:

```bash
git config user.name "Your Name"
git config user.email "your.email@example.com"
```

View your local configuration:

```bash
git config --local --list
```

## 2. Cloning a repository

There are two main protocols for cloning GitHub repositories: **HTTPS** and **SSH**.

  - **HTTPS**: simpler initial setup, but requires credential storage (token or keychain).
  - **SSH**: recommended, uses key-based authentication and simplifies multi-account management.

```bash
git clone <url>
```

You can define SSH aliases in `~/.ssh/config` for different account or
organizations.

> See: [Generate and Configure a Git SSH key](git-ssh.md).

## 3. Working with branches

### List branches

List all local and remote branches.

```bash
git branch -a
```

### Create a branch

Create and switch to a new branch:

```bash
git switch -c <branch-name>
```

### Switch branches

Move to another branch:

```bash
git switch <branch-name>
```

### Change a branch name

Change the active branch name:

```bash
git branch -m <new-branch-name>
```

### Delete a branch

Delete a branch that has already been merged:

```bash
git branch -d <branch-name>
```

Force delition (use with caution):

```bash
git branch -D <branch-name>
```

Delete a branch from remote:

```bash
git push origin --delete <branch-name>
```

## 4. Basic Workflow (Add -> Commit -> Push -> Pull)

### Check the repository status

See which files have been added, modified, or deleted:

```bash
git status
```

### Stage changes

Add specific files or all modifications to the staging area:

```bash
git add <file>
git add .
```

### Commit your changes

Create a new commit with a descriptive message:

```bash
git commit -m "feat: implement hit counter in monitor"
```

> Follow the [Conventinal Commits](https://www.conventionalcommits.org/en/v1.0.0/) format for consistency.

Amend the last commit (for example, typo fix a typo):

```bash
git commit --amend
```

### View commit history

List recent commits:

```bash
git log --oneline --decorate --graph --all
```

Show detailed information about a specific commit:

```bash
git show <commit-hash>
```

### Push changes to the remote repository

Send your commits to GitHub:

```bash
git push origin <branch-name>
```

### Pull and update your local branch

Update your branch with the latest remote commits:

```bash
git pull --ff-only origin <branch-name>
```

> Use `--ff-only` to ensure your history stays linear and clean.
> If Git wans about conflicts, resolve them, then commit the merge.

## 5. Git Tags

Tags are used to mark specific points in your project’s history, typically to identify releases (e.g., `v1.0`, `v2.1.3`).

They act like bookmarks to easily reference important commits.

### Semantic Versioning Guide

| Part    | When to increment                     | Example after change |
| ------- | ------------------------------------- | -------------------- |
| `MAJOR` | Breaking changes: old code won't work | `1.0.0` → `2.0.0`    |
| `MINOR` | New features, but backward-compatible | `1.0.0` → `1.1.0`    |
| `PATCH` | Bug fixes, small changes only         | `1.0.0` → `1.0.1`    |


### List tags

Display all tags in the repository:

```bash
git tag
```

### Create a lighweight tag

Lightweight tags simply label a commit:

```bash
git tag v1.0.0
```

> Lightweight tags are quick bookmarks but annotated tags are preferred for releases

### Create an annotated tag

Annotated tags include a message, the author's name, and a timestap.
They are **recommended** for official or shared releases.

```bash
git tag -a v1.0.0 -m "Version 1.0.0"
# or open your editor with:
git tag -a v1.0.0
```

### View details about a tag

Show detailed information about a specific tag (annotation, author, date, and commit):

```bash
git show v1.0.0
```

### Push tags to the remote repository

Tags are not pushed automatically.

Push a specific tag:

```bash
git push origin v1.0.0
```

Push all local tags:

```bash
git push origin --tags
```

### Delete tags

Delete a tag locally:

```bash
git tag -d v1.0.0
```

Delete a tag on the remote repository:

```bash
git push origin -d v1.0.0
```

## 6. Igniring files with `.gitignore`

The `.gitignore` file tells Git which files or directories to exclude from version control.
This helps keep your repository clean and prevents unnecessary or sensitive files from being committed.

### Create a `.gitignore` file

At the root of your repository, create a file named `.gitignore`:

```bash
touch .gitignore
```

Then open it and list the files or patterns to ignore, for example:

```plain
# Directories
build/
logs/

# Files
*.logs
*.bak

# Environments
.venv
```

## Reference Material

**Git**

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git Reference](https://git-scm.com/docs)
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet)

**GitHub**

- [GitHub's `.gitignore` templates](https://github.com/github/gitignore)
