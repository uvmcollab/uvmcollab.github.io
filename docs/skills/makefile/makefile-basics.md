---
draft: false
date: 2025-10-12
description: >
  Makefile Basics
authors: Ciro Bermudez
icon: material/file-cog
hide: 
#  - navigation
#  - toc
---

# :material-file-cog: Makefile Basics

A **Makefile** is a script that defines *how to build, test, and run tasks* using the **`make`** utility.
It automates repetitive shell commands through **targets**, **dependencies**, and **recipes**.

Makefiles are widely used in C/C++ projects, ASIC verification flows, and automation scripts because they are **simple, declarative, and dependency-aware**.

Ensure that you have install `make`:

```bash
make --version
```

## 1. Targets and rules

Each **rule** in a Makefile defines how to produce a *target* from its *dependencies*:

```makefile
tagets: dependencies
  command
  command
```

> **WARNING**: Makefiles **require tabs** for indentation, **do not use spaces**.

**Example:**

```makefile
compile: main.c utils.c
  gcc -o program main.c utils.c
```

- `target` -> the name you type after make
- `dependencies` -> files that must exist or be up to date
- `command` -> shell command executed (must start with a TAB)

Run it with:

```bash
make compile
```

> Targets aren’t limited to builds, they can also run scripts, run tests, clean directories, or perform checks.

## 2. Common Targets

| Option  | Description                                                         |
| ------- | ------------------------------------------------------------------- |
| `all`   | Default target that builds everything                               |
| `clean` | Removes generated files or build artifacts                          |
| `help`  | Print available targets (often implementd with comments and `grep`) |

### 2.1 `.PHONY` targets

The special target **`.PHONY`** is used to mark other targets as *not corresponding to real files*.
This ensures they **always runs**, even if a file with the same name exists.

**Example:**

```makefile
.PHONY: clean ## Remove all simulation files
  @echo "Removing all simulation files"
  rm -rf build/
```

> Use `.PHONY` for utility targets like `clean`, `help`, `compile`, `run`, and `all`.
> Without it, if a file named `clean` exists, `make clean` would be skipped.

> Add an `@` before a command to stop it from being printed

## 3. Command line Flags

| Option    | Description                                   |
| --------- | --------------------------------------------- |
| `-n`      | Print commands without executing them         |
| `-jN`     | Run N jobs in parallel                        |
| `-k`      | Keep going when some targets can't be made.   |
| `-C`      | Change to the directory before doing anything |
| `-f FILE` | Read FILE as a makefile                       |

Examples:

```makefile
make -j8 test      # Run 8 jobs in parallel for test target
make -n all        # Show what would be done without running it
make -C build run  # Run 'run' taget inside the build directory
```

## 4. Variables

This are some rules to take into consideration:

- Variables can only be strings
- Single or double quotes have no meaning to `make`
- Reference variables using either `$(VAR)` (preferred) or `${VAR}`
- Use `=` or `:=` to assign a value to a variable

### 4.1 Assignment types

| Syntax | Type               | Description                      |
| :----- | :----------------- | :------------------------------- |
| `=`    | Recursive (lazy)   | Value expanded **when used**     |
| `:=`   | Simple (immediate) | Value expanded **when defined**  |
| `?=`   | Conditional        | Assign only if not already set   |
| `+=`   | Append             | Add to existing variable content |

**Example:**

```makefile
# Directories
GIT_DIR     := $(shell git rev-parse --show-toplevel)
UVM_DIR     := $(GIT_DIR)/verification/uvm
ROOT_DIR    := $(CURDIR)

# Tool flags
VCS_FLAGS = -full64 -sverilog -ntb_opts uvm-1.2 -l $(CUR_DATE)_comp.log 
```

## 5. Automatic variables

Automatic variables represent file names or targets inside rules:

| Variable | Description                                                 |
| -------- | ----------------------------------------------------------- |
| `$@`     | Represents the target name                                  |
| `$<`     | Represents the first prerequisite                           |
| `$^`     | Represents all the prerequites                              |
| `$?`     | Represents the prerequisites that are newer than the target |

Example:

```makefile
%.o: %.c
  $(CC) $(CFLAGS) -c $< -o $@
```

## 6. Common variables for C/C++

| Variable               | Description                           |
| ---------------------- | ------------------------------------- |
| `CC` or `CXX`          | Variables for the C and C++ compilers |
| `CFLAGS` or `CXXFLAGS` | For compiler flags for the C and C++  |
| `LDFLAGS`              | Linker flags                          |
| `LBLIBS`               | Libraries to link against             |

## 7. Default shell

The default shell is `/bin/sh`.
To change it, override the `SHELL` variable:

```makefile
SHELL := bash
```

## 8. Change default target

Set the default goal to run when `make` is called with no arguments:

```makefile
.DEFAULT_GOAL := help
```

## References

**Tutorials**

- [Makefile Tutorial](https://makefiletutorial.com/)

**Official documentation**

- [GNU make Manual HTML](https://www.gnu.org/software/make/manual/make.html)
- [GNU make Manual PDF](http://gnu.org/software/make/manual/make.pdf)

**Books**

- [Managing Projects with GNU Make](http://uploads.mitechie.com/books/Managing_Projects_with_GNU_Make_Third_Edition.pdf)

**CMake**

- [CMake Tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)
