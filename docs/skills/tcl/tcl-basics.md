---
icon: material/medal
---

# Tcl Basics

## 0. Overview

Tcl stand for Tool Command Language. It is a small, dymanic scripting language
designed to be easy to embed and automate. It is widely know for:

- Simple syntax
- Strong string handling
- Dynamic typing
- Command-based structure
- Use with Tk for GUIs

## 1. Installation

To install Tcl you need to run:

```bash
brew update
brew install tcl-tk
```

```bash
sudo apt update
sudo apt install tcl
```

## 2. Mental Model

A Tcl program is made of commands.

```tcl
puts "Hello, world"
```

This means:

- command name: `puts`
- argument: "Hello, world"

Another example:

```tcl
set name "Ciro"
puts $name
```

- `set` assigns a variable
- `$name` reads a variable

## 3. Basic Syntax


## Reference Material

**Websites**

- [Tcl Developer Xchange](https://www.tcl-lang.org/doc/)
