---
icon: material/book-open-page-variant
---

# Compiler directives

Compiler directives are preprocessor command evaluated **before compilation**.
They control which parts of the code are included, excluded, or modified, and are
commonly used for **compile-time configuration**, **feature seclections**, and
**defensive coding**.

Unlike plusargs, compiler directives affect **what gets compiled**. not just how
the simulation behaves at runtime.

## Overview

SystemVerilog compiler directives start with a backtick (``` ` ```) and are processed
by the processor.

Commonly used directives include:

- ``` `define```  / ``` `undef```
- ``` `ifdef```   / ``` `ifndef```
- ``` `elsif```   / ``` `else```   / ``` `endif```
- ``` `include```
- ``` `default_nettype```

They are typically used to:

- Guard files from being compiled more than once
- Select alternative RTL or testbench implementations
- Enable or disable features at compile time
- Enforce strict coding rules

## Header Guard Pattern

A common pattern is to read plusargs at the start of the simulation and fall back to sensible
defaults when they are not provided.

```systemverilog
`ifndef MY_MODULE_SVH
`define MY_MODULE_SVH

// declarations, parameters, typedefs, macros

`endif // MY_MODULE_SVH
```

This ensures the file contents are processed **only once**, even if the file is
included multiple times.

!!! tip "Tips"

    Use a unique, uppercase macro name based on the file name.

## Conditional Compilation

Conditional compilation allows different code paths to be compiled based on
defined macros.

```systemverilog
`ifdef USE_FAST_MODEL
  fast_model u_model();
`else
  accurate_model u_model();
`endif
```

The macro can be defined:

- In source code:
```systemverilog
`define USE_FAST_MODEL
```
- Or from the command line:
```bash
vcs +define+USE_FAST_MODEL
```

## Multiple Conditions

```systemverilog
`ifdef SIMULATION
  // simulation-only code
`elsif SYNTHESIS
  // synthesis-specific code
`else
  // default implementation
`endif
```

Only **one branch** is compiled.

## Define Feature Defaults

A common pattern is to allow the command line to override defaults:

```systemverilog
`ifndef ENABLE_ASSERTIONS
  `define ENABLE_ASSERTIONS
`endif
```

This enables assertions by default, while still allowing them to be disabled:

```bash
vcs +define+ENABLE_ASSERTIONS=0
```

## Compile-Time Switches

Macros can be used to select RTL features that cannot be changed at runtime.

```systemverilog
`ifdef USE_PARITY
  logic parity_bit;
`endif
```

This code physically exists **only if** `USE_PARITY` is defined.

!!! warning

    Avoid using ``` `define``` for values that should be runtime-configurable.
    Use parameters or plusargs instead.

## Enforcing Explicit Declarations

By default, undeclared signals are implicitly created as `wire`, which can mask
typos and cause subtle bugs.

Recommended Setting:

```systemverilog
`default_nettype none
```

With this setting:

- All signals must be explicitly declared
- Typos result in **compile-time errors**

Example:

```systemverilog
assign out = inpt; // ERROR: 'inpt' not declared
```

## Restoring Default Behavior

When including legacy code, you may need to restore the default behavior:

```systemverilog
`default_nettype wire
```

!!! danger

    Always restore the nettype after including third-party or legacy code to avoid leaking settings into other files.

## Recommended File Pattern

A commonly used safe pattern is:

```systemverilog
`ifndef MY_MODULE_SV
`define MY_MODULE_SV

`default_nettype none

module my_module (
  input  logic clk_i,
  input  logic rst_I,
  output logic out_o
);
  // module implementation
endmodule

`default_nettype wire

`endif // MY_MODULE_SV
```

This combines:

- Compilation guards
- Strict signal declaration
- Controlled scope

## Best Practices

!!! tip "Tips"

    - Use compilation guards for all include files
    - Prefer ``` `default_nettype none``` in all new code
    - Limit the scope of compiler directives
    - Document macros that affect compilation
    - Avoid deep nesting of ``` `ifdef``` blocks
    - Separate simulation, synthesis, and debug code clearly

## Reference Material

**LRM**

- [IEEE 1800-2023: 22 Compiler directives](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 22.4 ``` `include```](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 22.4 ``` `include```](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 22.6 ``` `ifdef``` ``` `else```, ``` `elsif```, ``` `endif```, ``` `ifndef```](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 22.8 ``` `default_nettype```](https://ieeexplore.ieee.org/document/10458102)

**Websites**

- [ZipCPU: Verilog Tutorial - Wires, and combinatorial logic (page 26)](https://zipcpu.com/tutorial/lsn-01-wires.pdf#page=29)
