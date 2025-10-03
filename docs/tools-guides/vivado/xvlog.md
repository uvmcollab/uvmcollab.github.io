---
draft: false
date: 2025-09-30
description: >
  Xilinx xvlog
authors: Ciro Bermudez
icon: material/hammer
hide: 
#  - navigation
#  - toc
---

# :material-hammer: XVLOG Flags

## **General Flags**

| Flag                  | Description                                                                                                                                                                             |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-h`                  | Print the help message                                                                                                                                                                  |
| `-f <filename>`       | Read additional options form the specified file                                                                                                                                         |
| `--version`           | Print the compiler version                                                                                                                                                              |
| `-log <filename>`     | Specify the log file name. Default `<xvlog.log>`.                                                                                                                                       |
| `-f <filename>`       | Read additional options from the specified file.                                                                                                                                        |
| `-i <directory_name>` | Specify directories to be searched for files included using `include`. Use `-i` for each specifies search directory.                                                                    |
| `-d`                  | Define Verilog macros. Use `-d` for each Verilog macro. The format of the macro is `<name>[=<val>]`.                                                                                    |
| `--work <name>`       | Specify the work library. The format of the argument is `<name>[=<dir>]` where `<name>` is the logical name of the library and `<dir>` is an optional physical directory of the library |
| `-sv`                 | Compile input files in System Verilog mode                                                                                                                                              |

