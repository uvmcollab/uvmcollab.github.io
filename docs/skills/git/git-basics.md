---
draft: false
date: 2025-10-12
description: >
  Git Basics
authors: Ciro Bermudez
icon: material/git
hide: 
#  - navigation
#  - toc
---

# :material-git: Git Basics

## Git Basics

Ensure that the version of your Git Installation is > 1.8 with:

```bash
git --version
```

## Global configurations

The first time you use `git` you need to configure:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

You can check your configuration at any time with:

```bash
git config --list
```

## Local configurations

If you work with multiple GitHub/GitLab account you can set a
name and an email for a specific repository without changing
the global Git configuration with:

```bash
git config user.name "Your Name"
git config user.email "your.email@example.com"
```

You can check your local configurations at any time with:

```bash
git config --local --list
```

## Branches

You can create a new branch with:

```bash
git branch branch-name
```

## Reference Material

### Git

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet)