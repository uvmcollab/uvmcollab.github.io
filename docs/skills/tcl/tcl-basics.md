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

=== "macOs"
    ```bash
    brew update
    brew install tcl-tk
    ```
    
=== "Linux"
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

### Words and commands

A command is usually one line:

```tcl
set x 10
puts $x
```

You can also separete commands with `;`:

```tcl
set x 10; puts $x
```

### Variable substitution with `$`

```tcl
set name "Alice"
puts $name
```

### Command substitution `[]`

The result of a command can be inserted into another command:

```tcl
set len [string length "hello"]
puts $len
```

### Grouping with braces `{}`

Braces usually prevent substitution:

```tcl
set x 5
puts {$s}
```

this prints `$x`, not `5`.

### Grouping with quoutes `""`

Quotes allow substitution:

```tcl
set x 5
puts "x is $x"
```

## 4. Variables

```tcl
set age 25
puts $age
```

Change a variable:

```tcl
set age 26
```

Unset it:

```tcl
unset age
```

## 5. Lists

Lists are gundamental in Tcl.

```tcl
set fruits {apple banana orange}
puts [lindex $fruits 0]
```

Useful list commands:

```tcl
llength $fruits
lindex $fruits 1
lappend fruits grape
```

Example:

```tcl
set nums {1 2 3}
lappend nums 4
puts $nums
```

## 6. Math

Use `expr` for arithmetic.

```tcl
set a 10
set b 3
puts [expr {$a + $b}]
puts [expr {$a * $b}]
puts [expr {$a / $b}]
```

Use braces with `expr`:

```tcl
expr {$a + $b}
```

That is the normal safe style.

## 7. Strings

```tcl
set s "hello world"
puts [string length $s]
puts [string toupper $s]
puts [string first "world" $s]
```

Common string commands:

- `string length`
- `string toupper`
- `string tolower`
- `string trim`
- `string first`
- `string range`


## 8. Conditionals

```tcl
set x 10

if {$x > 5} {
    puts "big"
} else {
    puts "small"
}
```

`elseif` works too:

```tcl
if {$x < 0} {
    puts "negative"
} elseif {$x == 0} {
    puts "zero"
} else {
    puts "positive"
}
```

## 9. Loops

### `while`

```tcl
set i 0
while {$i < 5} {
    puts $i
    incr i
}
```

### `for`

```tcl
for {set i 0} {$i < 5} {incr i} {
    puts $i
}
```

### `foreach`

```tcl
foreach item {apple banana orange} {
    puts $item
}
```

## 10. Procedures

Procedures are functions.

```tcl
proc greet {name} {
    puts "Hello, $name"
}

greet "Alice"
```

Return a value:

```tcl
proc add {a b} {
    return [expr {$a + $b}]
}

puts [add 2 3]
```

## 11. Scope

Variables inside a procedure are local by default.

```tcl
set x 100

proc test {} {
    set x 5
    puts $x
}

test
puts $x
```

If you need a global variable:

```tcl
set counter 0

proc incCounter {} {
    global counter
    incr counter
}
```

## 12. Arrays

Tcl arrays are key-value maps.

```tcl
set person(name) "Alice"
set person(age) 30

puts $person(name)
puts $person(age)
```

Loop over keys:

```tcl
foreach key [array names person] {
    puts "$key = $person($key)"
}
```

## 13. Dictionaries

Modern Tcl often uses dictionaries.

```tcl
set d [dict create name Alice age 30]
puts [dict get $d name]
puts [dict get $d age]
```

Update:

```tcl
dict set d city Rome
```

## 14. File I/O

Write a file:

```tcl
set f [open "test.txt" w]
puts $f "Hello file"
close $f
```

Read a file:

```tcl
set f [open "test.txt" r]
set content [read $f]
close $f
puts $content
```

## 15. Comments

```tcl
# This is a comment
set x 10
```


## 16. Mini Cheat Sheet

```tcl
set x 10
puts $x
puts [expr {$x + 5}]

if {$x > 5} {
    puts "yes"
}

foreach item {a b c} {
    puts $item
}

proc square {n} {
    return [expr {$n * $n}]
}

puts [square 4]
```

## 17. Documentation-Style Summary

Core concepts:

- Syntax is command + arguments.
- Variables use `$name`.
- Command results use `[command ...]`.
- Braces `{}` suppress substitution.
- Quotes `""` allow substitution.
- Arithmetic goes through `expr`.
- Functions are defined with `proc`.
- Lists, arrays, and dictionaries are key data tools.

## Reference Material

**Websites**

- [Tcl Developer Xchange](https://www.tcl-lang.org/doc/)
