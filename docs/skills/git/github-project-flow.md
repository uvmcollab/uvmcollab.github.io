---
icon: material/github
---

# GitHub Project Flow

A concise guide to move efficiently from **Project Issue → Branch → Pull Request → Merge**, keeping full traceability between your code and project tasks.

## 1. From GitHub Project to Branch

1. **Create or select and Issue in your GitHub Project**
    - Open your **GitHub Project Board -> Backlog** column
    - Click **+ Add item -> Create a new issue**
    - Select the target **Repository** and **Template**
    - Fill the following fields:
        - **Title**: short, clear
        - **Description**: context and motivation
        - **Assignee**: yourself or a team member (if you are the manager)
        - **Issue Type (optional)**: Bug, Feature, Task
2. **Create a linked branch directly from the Issue**
      - In the Issue page, locate the **Development** panel (right sidebar)
      - Click **Create a branch**
      - Use a clear and consisten banch name, for example:
      ```bash
      feat/uvc-monitor-logic
      ```
      - GitHub automatically links the branch to the issue (traceability maintained)
3. **Checkout the branch locally**
```bash
git fetch origin
git checkout feat/uvc-monitor-logic
```
4. **Work, add, and commit your changes**
    - Follow [Conventinal Commits](https://www.conventionalcommits.org/en/v1.0.0/) to maintain clarity and consistency
```bash
git add gpio_uvc_monitor.sv
git commit -m "feat: add gpio_uvc monitor logic"
```
5. **Push your branch to GitHub**
```bash
git push -u origin feat/uvm-monitor-logic
```
6. **Open Pull Request (PR) in GitHub**
    - GitHub usually suggests this automatically
    - Make sure the PR title is clear and follows [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
    - Add a concise description summarizing the changes
    - Push additional commits as needed before merging
7. **Merge Pull Request**
    - Wait for review and approval
    - Merge using your team's policy (Squash, Merge or Rebase)
    - Click **Delete branch** in GitHub after merging
8. **Clean up locally and prune stale remotes**
```bash
git switch main
git fetch --prune origin
git branch -D feat/uvm-monitor-logic
```
9. **Sync your local `main` branch**
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

**GitHub**

- [Planning and tracking with Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects)
