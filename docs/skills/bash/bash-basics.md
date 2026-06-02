---
icon: simple/gnubash
---

# Bash Basics

## 1. What is a Bash script?

A Bash script is a text file with terminal command executed in order.

Example:

```bash
#!/usr/bin/env bash

printf "Hello, World\n"
```

The first line is called the **shebang**:

```bash
#!/usr/bin/env bash
```

It tell Linux to run the file using Bash.

To execute the script:

```bash
chmod +x script.sh
./script.sh
```

## 2. Basic script structure

A clean Bash script can look like this:

```bash
#!/usr/bin/env bash

set -euo pipefail

main() {
    printf 'Running script...\n'
}

main "$@"
```

Explanation:

```bash
set -euo pipefail
```

| Option        | Meaning                                         |
| ------------- | ----------------------------------------------- |
| `-e`          | Exit immediately if a command fails             |
| `-u`          | Treat undefined variables as errors             |
| `-o pipefail` | A pipeline fails if any command inside it fails |

## 3. Variables

Declare variables without spaces around `=`:

```bash
name="Ciro"
project="uvm_testbench"

printf "Name: %s\n" "$name"
printf "Project: %s\n" "$project"
```

Important:

```bash
name = "Ciro" # Wrong
name="Ciro"   # Correct
```

!!! warning
    Use quotes when expanding variables. This avoids problems with spaces or empty values.

## 4. Script arguments

Arguments are values passed when launching the script:

Example:

```bash
./script.sh run_dir coverage.vdb
```

Inside the script:

```bash
#!/usr/bin/env bash
set -euo pipefail

RUN_DIR="$1"
COV_DB="$2"

printf 'Run directory: %s\n' "$RUN_DIR"
printf 'Coverage DB: %s\n' "$COV_DB"
```

Special argument variables:

| Variable        | Meaning                         |
| --------------- | ------------------------------- |
| `$0`            | Script name                     |
| `$1`, `$2`, ... | First, second, etc. argument    |
| `$#`            | Number of arguments             |
| `$@`            | All arguments, safely separated |

## 5. Required arguments with useful errors

This pattern is very useful:

```bash
RUN_DIR="${1:?missing RUN_DIR}"
```

Meaning:

- Use argument `$1`
- If `$1` is missing or empty, stop the script
- Print the error `missing RUN_DIR`

Example:

```bash
#!/usr/bin/env bash
set -euo pipefail

RUN_DIR="${1:?missing RUN_DIR}"

printf 'RUN_DIR = %s\n' "$RUN_DIR"
```

If you run:

```bash
./script.sh
```

Bash will stop with an error.

```plain
missing RUN_DIR
```

## 6. Default values

Use this syntax to provide a default value:

```bash
MODE="${MODE:-debug}"
```

Meaning:

- If `MODE` is defined and not empty, use it
- Otherwise, use `debug`

Example:

```bash
#!/usr/bin/env bash
set -euo pipefail

MODE="${MODE:-debug}"

printf 'Mode: %s\n' "$MODE"
```

Run normally:

```bash
./script.sh
```

Output:

```plain
Mode: debug
```

Run with a custom value:

```bash
MODE=regression ./script.sh
```

Output:

```plain
Mode: regression
```

## 7. Checking files and directories

Bash has useful test operators.

```bash
if [[ -d "$RUN_DIR" ]]; then
    printf 'Directory exists: %s\n' "$RUN_DIR"
fi
```

Common checks:

| Check        | Meaning                           |
| ------------ | --------------------------------- |
| `-d "$path"` | Path exists and is a directory    |
| `-f "$path"` | Path exists and is a regular file |
| `-e "$path"` | Path exists                       |
| `-z "$var"`  | String is empty                   |
| `-n "$var"`  | String is not empty               |

Negation uses `!`:

```bash
if [[ ! -d "$RUN_DIR" ]]; then
    printf '[FAIL] Directory does not exist: %s\n' "$RUN_DIR"
    exit 1
fi
```

Meaning:

```bash
! -d "$RUN_DIR" 
```

means:

> If `RUN_DIR` is not a directory

## 8. Functions

Functions help organize your script.

```bash
print_error() {
    local msg="$1"
    printf '[FAIL] %s\n' "$msg"
}

print_error "Something went wrong"
```

Use `local` for variables inside functions:

```bash
check_dir() {
    local dir="${1:?missing dir}"

    if [[ ! -d "$dir" ]]; then
        printf '[FAIL] Directory does not exist: %s\n' "$dir"
        return 1
    fi
}
```

Then call it:

```bash
check_dir "$RUN_DIR"
```

## 9. Reading command output into a variable

The `$()` syntax in Bash is used for command substitution. It allows you to execute a
command and substitute its output in place of the `$()` expression. This is useful
when you want to use the output of a command as an argument for another command
or assign it to a variable.

Example:

```bash
latest_file="$(
    find "$RUN_DIR" -type f -name '*.log' |
    sort |
    tail -n 1
)"
```

## 10. Reading values from a file with `source`

Suppose you have a file called `coverage.mk`

```bash
RUN_COV_DB=/path/to/run/simv.vdb
BUILD_COV_DB=/path/to/build/simv.vdb
```

You can load it is Bash with:

```bash
source $$RUN_MANIFEST_FILE"
```

After that, the variables are available:

```bash
printf 'RUN_COV_DB = %s\n' "$RUN_COV_DB"
```

Example:

```bash
#!/usr/bin/env bash
set -euo pipefail

RUN_MANIFEST_FILE="${1:?missing RUN_MANIFEST_FILE}"

if [[ ! -f "$RUN_MANIFEST_FILE" ]]; then
    printf '[FAIL] Manifest file does not exist: %s\n' "$RUN_MANIFEST_FILE"
    exit 1
fi

source "$RUN_MANIFEST_FILE"

if [[ -z "${RUN_COV_DB:-}" ]]; then
    printf '[FAIL] RUN_COV_DB is empty\n'
    exit 1
fi

printf 'RUN_COV_DB = %s\n' "$RUN_COV_DB"
```

## Reference Material

**Websites**

- [Bash Guide](https://mywiki.wooledge.org/BashGuide)
- [Bash Pitfalls](https://mywiki.wooledge.org/BashPitfalls)

**Books**

- [Bash Cookbook](https://bashcookbook.com/)