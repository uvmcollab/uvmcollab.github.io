---
icon: fontawesome/solid/ruler
---

# UVM HDL Backdoor Access

These routines provide an interface to the DPI/PLI implementation of
backdoor access used by registers.

## `uvm_hdl_read()`

```systemverilog linenums="1"
import "DPI-C" context function int uvm_hdl_read(
            string          path,
    output  uvm_hdl_data_t  value
)
```

Gets the value at the given path. Returns 1 if the call succeeded, 0 otherwise.

## `uvm_hdl_deposit()`

```systemverilog linenums="1"
import "DPI-C" context function int uvm_hdl_deposit(
    string          path,
    uvm_hdl_data_t  value
)
```

Sets the given HDL path to the specified value. Returns 1 if the call succeeded, 0 otherwise.

## `uvm_hdl_force()`

```systemverilog linenums="1"
import "DPI-C" context function int uvm_hdl_force(
    string          path,
    uvm_hdl_data_t  value
)
```

Forces the value on the given path. Returns 1 if the call succeeded, 0 otherwise.

## `uvm_hdl_release()`

```systemverilog linenums="1"
import "DPI-C" context function int uvm_hdl_release(
    string  path
)
```

Releases a value previously set with [`uvm_hdl_force`](#uvm_hdl_force).  Returns 1 if the call succeeded, 0 otherwise.

## Reference Material

**Accellera**

- [UVM 1.2 Class Reference UVM HDL Backddor Access supoort routines](https://verificationacademy.com/verification-methodology-reference/uvm/docs_1.2/html/files/dpi/uvm_hdl-svh.html)
- [UVM 1.2 Class Reference Index](https://verificationacademy.com/verification-methodology-reference/uvm/docs_1.2/html/index.html)

**Source Code**

- [Source code `uvm_hdl.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/dpi/uvm_hdl.svh)
