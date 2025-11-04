---
draft: false
date: 2025-10-12
description: >
  Git Development Flow
authors: Ciro Bermudez
icon: material/git
hide: 
#  - navigation
#  - toc
---

# :material-git: Git Development Flow

A straightforward, day-to-day Git workflow to move from:
**Branch creation → Development → Pull request → Merge**.

## 1. Standard Development Flow

1. **Create a new branch**
    - Use clear and consistent branch names, for example: 
```bash
git switch -c feat/uvc-monitor-logic
```
2. **Work, add, and commit your changes** 
    - Follow [Conventinal Commits](https://www.conventionalcommits.org/en/v1.0.0/) to maintain clarity and consistency
```bash
git add gpio_uvc_monitor.sv
git commit -m "feat: add gpio_uvc monitor logic"
```
3. **Push your branch to GitHub**
```bash
git push -u origin feat/uvm-monitor-logic
```
4. **Open Pull Request (PR) in GitHub**
    - GitHub usually suggests this automatically
    - Make sure the PR title is clear and follows [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
    - Add a concise description summarizing the changes
    - Push additional commits as needed before merging
5. **Merge Pull Request**
    - Wait for review and approval
    - Merge using your team's policy (Squash, Merge or Rebase)
    - Click **Delete branch** in GitHub after merging
6. **Clean up locally and prune stale remotes**
```bash
git switch main
git fetch --prune origin
git branch -D feat/uvm-monitor-logic
```
7. **Sync your local `main` branch**
```bash
git pull --ff-only origin main
```

## 2. Bring changes from `main` into a feature branch

When your feature branch becomes outdated, update it with the latest commits from `main`:

```bash
git switch feat/branch
git fetch origin main
git merge origin/main
# resolve conflicts if any, then commit
```

## Reference Material

**Git**

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git Reference](https://git-scm.com/docs)
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet)

**Guidelines**

- [Conventinal Commits](https://www.conventionalcommits.org/en/v1.0.0/)
