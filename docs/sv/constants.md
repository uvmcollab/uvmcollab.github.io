---
icon: material/lock
---

# Constants

Constants are named data objects whose value **cannot change** after they are defined.
SystemVerilog provides both **elaboration-time constants** and **run-time constants**.

| Keyword      | Scope                      | Overridable | Typical Usage                      |
| ------------ | -------------------------- | ----------- | ---------------------------------- |
| `parameter`  | Module / Interface / Class | Yes         | Configurable design parameters     |
| `localparam` | Module / Interface / Class | No          | Internal constants, derived values |

#### `parameter`

- Value can be **overridden** at instantiation
- Commonly used for **design configuration**
- Participates in generate constructs

Example:

```systemverilog
module fifo #(
  parameter int DEPTH = 16
);
```

### `localparam`

- Value **cannot be overridden**
- Used to protect internal constants
- Often derived from other parameters

Example:

```systemverilog
localparam int ADDR_W = $clog2(DEPTH);
```

### `const`

Run-time constants are evaluated **during simulation**, but once assigned,
their value **cannot be modified**.

- Enforces read-only semantics
- Value is set at declaration or construction
- Common in testbenches and classes

Example:

```systemverilog
class cfg;
  const int unsigned NUM_LANES = 4;
endclass : cfg
```

### Summary

| Feature                         | `parameter` | `localparam` | `const`   |
| ------------------------------- | ----------- | ------------ | --------- |
| Time of evaluation              | Elaboration | Elaboration  | Run-time  |
| Can be overridden               | Yes         | No           | No        |
| Can change after initialization | No          | No           | No        |
| Typical domain                  | RTL / TB    | RTL / TB     | Mostly TB |

!!! tip "Tips"

    - Use `parameter` for externally configurable values,
    - `localparam` for internal derived constants
    - and `const` to enforce read-only behavior in run-time objects and classes.

## Reference Material

**LRM**

- [IEEE 1800-2023: 6.20 Constants](https://ieeexplore.ieee.org/document/10458102)
