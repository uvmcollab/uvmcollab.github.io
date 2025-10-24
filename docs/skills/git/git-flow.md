---
draft: false
date: 2025-10-12
description: >
  Git Flow
authors: Ciro Bermudez
icon: material/git
hide: 
#  - navigation
#  - toc
---

# :material-git: Git development flow

## Git development flow

Use Git [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)

1. Create branch

```bash
git switch -c feat/uvc-monitor-logic
# ot
git branch feat/uvc-monitor-logic && git switch feat/uvc-monitor-logic
```

2. Work, add, commit (small commits, Conventional Commits)

```bash
git add monitor.sv
git commit -m "feat: add monitor logic"
```

3. Push your branch to GitHub

```bash
git push -u origin feat/uvm-monitor-logic
```

4. Open **Pull Request** in GitHub.

   - Use a clear Conventional Commit PR tittle
   - Push more commits as needed

5. Merge **Pull Request**

    - Use yout team's choice (Merge commits/Squash)
    - Click **Delete branch** in GitHub

6. Clean up locally and prune stale remotes

```bash
git switch main
git fetch --prune origin
git branch -D feat/uvm-monitor-logic
```

7. Sync your local `main` branch (fast-forward only)

```bash
git pull --ff-only origin main
```

## Bring changes from `main` into a feature branch

When working on a feature branch, you often need to update it with the latest changes from `main`.
The recommended approach is:

```bash
git switch feat/branch
git fetch origin main
git merge origin/main
# resolve conflicts if any, then commit
```

## Reference Material

### Git

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet)
