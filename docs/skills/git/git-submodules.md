---
draft: false
date: 2025-10-12
description: >
  Git Submodules
authors: Ciro Bermudez
icon: material/git
hide: 
#  - navigation
#  - toc
---

# :material-git: Git Submodules

Submodules allow you to include one Git repository inside another.  They are commonly used when your project depends on **shared libraries**, **frameworks**, or **verification components** that are developed in separate repositories.

## 1. When to use Submodules

Use submodules when:

- You need to **reuse another repository** inside your project.
- Each submodule should **keep its own history** and versioning.
- You want to **pin the dependency to a specific commit** (not always the latest).

Avoid submodules if:

- You just need to share simple files or scripts (use subtrees or packages instead).
- The external project changes frequently, it may complicate updates for collaborators.

## 2. Add a Submodule

From your main project repository, make sure you are in the root:

```bash
git submodule add <repository-url> <path>
```

Example:

```bash
git submodule add git@github.com:uvmcollab/gpio_uvc.git uvcs/gpio_uvc
```

Git creates a folder at `uvcs/gpio_uvc/` and records the submodule reference in `.gitmodules`, this file is created if is the first submodule or updated.

The `.gitmodules` file is versioned, it stores the submodule’s path and URL so
collaborators can clone it easily. It is a good practice to commit the changes
after adding a submodule, meaning adding both `.gitmodule` and `uvcs/gpio_uvc`:

```bash
git add .gitmodules uvcs/gpio_uvc
git commit -m "feat: add gpio_uvc as a submodule"
```

## 3. Initalize and Update Submodules


## Reference Material

**Git**

- [Pro Git Book](https://git-scm.com/book/en/v2)
    - [7.11 Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [Git Reference](https://git-scm.com/docs)
    - [Git Reference: git-submodule](http://git-scm.com/docs/git-submodule)
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet)

**GitHub**
