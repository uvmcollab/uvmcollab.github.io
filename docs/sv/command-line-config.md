---
icon: material/book-open-page-variant
---

# Command Line Configuration

Command line configuration allows a SystemVerilog testbench to be **parameterized at runtime**,
without recompiling the simulation. This is commonly used to control iteration count, timeouts,
delays, debug features, or experimental knobs directly from the simulator command line.

This mechanism is known as **plusargs**.

## Overview

Plusargs are command-line arguments passed to the simulator using the `+` prefix:

```bash
./simv +iterations=1000 +sim_type="experimental"
```

Inside the testbench, these arguments can be queried using built-in SystemVerilog
system functions such as:

```systemverilog
$test$plusargs(string)
$value$plusargs(user_string, variable)
```

- `$test$plusargs(string)` checks whether a specific plusarg is present.
- `$value$plusargs(user_string, variable)` extracts a typed value from the command line and assigns it to a variable.

This approach enables flexible testbench configuration, making it easy to run
multiple scenarios, sweep parameters, or enable debug features without modifying
or recompiling the testbench code.

## Practical example

A common pattern is to read plusargs at the start of the simulation and fall back to sensible
defaults when they are not provided.

```systemverilog
int unsigned iterations = 50;

function void get_config_args();
  int unsigned cli_value;

  if ($value$plusargs("iterations=%d", cli_value)) begin
    iterations = cli_value;
    $display("[INFO] %10t: iterations = %5d", $realtime, iterations);
  end else begin
    $display("[INFO] %10t: iterations = %5d (DEFAULT)", $realtime, iterations);
  end

endfunction : get_config_args
```

Run the simulation with:

```bash
./simv +iterations=500
```

This pattern makes the testbench **self-documenting** and avoids hard-coding simulation
parameters.

## Boolean Flags

For feature toggles or debug switches, `$test$plusargs()` is often sufficient:

```systemverilog
bit enable_debug;

initial begin
  enable_debug = $test$plusargs("debug");

  if (enable_debug)
    $display("[INFO] Debug mode enabled");
end
```

Usage:

```bash
./simv +debug
```

## Supported Format Specifiers

The format specifiers follow the same rules as the `$display` system task and control how the value is converted.


| Format | Meaning                         | Example plusarg   |
| :----: | ------------------------------- | ----------------- |
|  `%d`  | Decimal integer                 | `+iterations=100` |
|  `%o`  | Octal integer                   | `+mask=17`        |
|  `%h`  | Hexadecimal integer             | `+addr=3F`        |
|  `%b`  | Binary integer                  | `+enable=1`       |
|  `%x`  | Hexadecimal integer             | `+addr=3f`        |
|  `%e`  | Real (exponential notation)     | `+gain=1.0e-3`    |
|  `%f`  | Real (decimal notation)         | `+threshold=0.75` |
|  `%g`  | Real (auto decimal/exponential) | `+scale=10.0`     |
|  `%s`  | String (no conversion)          | `+mode=fast`      |

!!! note
    Uppercase/lowercase variants and leading zeros are accepted.

## Best Practices

!!! tip "Tips"

    - **Provide defaults**: Always define a safe default value if a plusarg is missing.
    - **Log resolved values**: Print the final configuration at startup to make runs reproducible.
    - **Centralize parsing**: Parse all plusargs in one place (e.g. a config object or base test).
    - **Use clear naming**: Prefer descriptive names for plusargs.
    - **Avoid overuse**: Plusargs are ideal for runtime tuning—not for structural configuration that belongs in parameters or classes.

## Reference Material

**LRM**

- [IEEE 1800-2023: 21.6 Command Line Input](https://ieeexplore.ieee.org/document/10458102)
