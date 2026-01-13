---
icon: material/git
---

# Git Submodules

Git **submodules** let you include one repository inside another.
They are commonly used when a project depends on **shared libraries**, **frameworks**, or **verification components** maintained in separate repositories.

## 1. When to use Submodules

Use submodules when:

- You need to **reuse another repository** inside your project.
- Each submodule should **keep its own commit history** and versioning.
- You want to **pin the dependency to a specific commit** instead of always using the latest version.

Avoid submodules if:

- You only need a few shared scripts or files, consider **direct copies** instead.
- The external repository changes frequently and you expect contributors to sync often.

## 2. Add a Submodule

From the root of your main project repository:

```bash
git submodule add <repository-url> <path>
```

Example:

```bash
git submodule add git@github.com:uvmcollab/gpio_uvc.git uvcs/gpio_uvc
```

Git creates a folder at `uvcs/gpio_uvc/` and records the submodule reference in a new or existing `.gitmodules` file.

Commit both the submodule directory and the `.gitmodules` file.

```bash
git add .gitmodules uvcs/gpio_uvc
git commit -m "feat: add gpio_uvc as a submodule"
```

The `.gitmodules` file is version-controlled and contraines the submodule's path and URL.
This allows collaborator to clne and initialize submodules automatically

## 3. Initalize and Update Submodules

When you clone a repository that contains submodules, Git only creates the submodule directories,
it doesn’t fetch their contents by default.

To initialize all submodules and check out the correct commits:

```bash
git submodule update --init --recursive
```

- `--init` ensures local configuration is created for each submodule.
- `--recursive` also initializes nested submodules if any exist.

## 4. Update submodules to the lastest version

To update **a single submodule** to the latest commit on its tracked branch:

```bash
cd uvc/gpio_uvc
git fetch origin
git checkout main
git pull origin main
cd ../../
git add uvc/gpio_uvc
git commit -m "chore: update gpio_uvc module to latest version"
```

Updating a submodule changes the commit pointer stored in the main repository.
Always commit this change so collaborators use the same submodule revision.

To update **all submodules** at once:

```bash
git submodule update --remote --merge
```

## 5. The `.gitmodules` file

Each submodule is listed in `.gitmodules`:

```ini
[submodule "uvcs/gpio_uvc"]
  path = uvcs/gpio_uvc
  url = git@github.com:uvmcollab/gpio_uvc.git
  branch = main
```

> Keep `.gitmodules` under version control, it defines how every collaborator's setups stays
> consistent.

## Reference Material

**Git**

- [Pro Git Book](https://git-scm.com/book/en/v2)
    - [7.11 Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [Git Reference](https://git-scm.com/docs)
    - [Git Reference: git-submodule](http://git-scm.com/docs/git-submodule)
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet)