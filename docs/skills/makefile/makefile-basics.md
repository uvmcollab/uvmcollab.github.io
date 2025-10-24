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

## Summary

## Command line Flags

| Option    | Description                                      |
| --------- | ------------------------------------------------ |
| `-j [N]`  | Allows N jobs at once; infinite jobs with no arg |
| `-f FILE` | Read FILE as a makefile                          |
| `-k`      | Keep going when some targets can't be made.      |
| `-h`      | Print help and exit                              |
| `-i`      | Adds a `-` to every command                      |
| `-C`      | Change to the directory before doing anything    |

## Common Targets

| Option   | Description                                                                                                                            |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `.PHONY` | Used to declare targets that don't represent output files, ensuring they're always executed regardless of file existence or timestamps |
| `all`    | Typically used to specify the default target                                                                                           |
| `clean`  | Conventionally used to define rules for cleaning up the project directory by removing generated files or artifacts                     |

## Common variables for C/C++

| Variable               | Description                           |
| ---------------------- | ------------------------------------- |
| `CC` or `CXX`          | Variables for the C and C++ compilers |
| `CFLAGS` or `CXXFLAGS` | For compiler flags for the C and C++  |

## References

### Tutorials

- [Makefile Tutorial](https://makefiletutorial.com/)

### Official documentation

- [GNU make Manual HTML](https://www.gnu.org/software/make/manual/make.html)
- [GNU make Manual PDF](http://gnu.org/software/make/manual/make.pdf)

### Books

- [Managing Projects with GNU Make](http://uploads.mitechie.com/books/Managing_Projects_with_GNU_Make_Third_Edition.pdf)

### CMake

- [CMake Tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)