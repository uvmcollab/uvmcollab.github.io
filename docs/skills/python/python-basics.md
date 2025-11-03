---
draft: false
date: 2025-10-13
description: >
  Python Basics
authors: Ciro Bermudez
icon: material/language-python
hide: 
#  - navigation
#  - toc
---

# :material-language-python: Python Basics

This note summarizes key Python conventions and setup topics that are frequently encountered in project scripts, automation tools, and reproducible environments.

## 1. The Shebang line

A **shebang** (also called a *hashbang*) is the first line in a script that tells the operating system **which interpreter** should run the file.

```python
#!/usr/bin/env python3
```

This line means:

- Use the `python3` interpreter found in the user’s `$PATH`
- Allow the script to be run directly

```bash
chmod +x script.py
./script.py
```

> Use `#!/usr/bin/env python3` instead of a hard-coded path like `/usr/bin/python3`
> it makes the script portable across systems and virtual environments.

## 2. Why `if __name__ == "__main__":`

Every Python file has a built-in variable called `__name__`

- When the file is executed directly, Python sets `__name__ = "__main__"`
- When the file is imported as a module, `__name__` becomes the module’s name

This allows a script to define reusable functions or classes, while keeping its *main* behavior isolated:

```python
def main():
    print("Running main script logic")

if __name__ == "__main__":
    main()
```

Why use it:

- Avoids running code unintentionally when the file is imported.
- Keeps code modular and testable.
- Defines a clear entry point for scripts.

## 3. Creating a virtual environment

A **virtual environment** isolates project dependencies, ensuring each project uses its own packages and versions.

Create one in your project directory:

```bash
python3 -m venv .venv
```

Activate it:

```bash
source .venv/bin/activate
```

Once activated:

- Your shell prompt changes to show `(.venv)`
- All `pip install` commands affect only this environment

To deactivate:

```bash
deactivate
```

> It’s common to name the environment folder `.venv` so it stays hidden and can be added to `.gitignore`.

## 4. The pyproject.toml file

The `pyproject.toml` file is a standardized configuration file that describes your
Python project’s **build system, dependencies, and metadata**.

It replaces older tools like `setup.py` and `requirements.txt` for modern workflows.

```toml
[project]
name = "project_name"
version = "0.1.0"
description = "Project description"
authors = [
  { name = "author name", email = "author@example.com" }
]
dependencies = [
  "numpy >=1.26"
]

[build-system]
requires = ["hatchling >= 1.26"]
build-backend = "hatchling.build"
```

To install dependencies and enable editable installs:

```bash
python3 -m pip install -e .
```

> `pyproject.toml` is part of PEP 518 and PEP 621
> Many modern tools (Poetry, Flit, Hatch, Ruff, Black) rely on it as a single unified configuration file.

## References

**PyPI**

- [Packaging Python Projects](https://packaging.python.org/en/latest/tutorials/packaging-projects/)

**PEP**

- [PEP 621](https://peps.python.org/pep-0621/)
- [PEP 518](https://peps.python.org/pep-0518/)
