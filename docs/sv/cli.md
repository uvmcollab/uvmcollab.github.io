---
icon: material/book-open-page-variant
---

# Command Line Configuration

Command line configuration allows a SystemVerilog testbench to be **parameterized at runtime**,
without recompiling the simulation. This is commonly used to control iteration count, timeouts,
delays, debug features, or experimental knobs directlry from the simulator command line.

This mechanism is known as **plusargs**

## Overview

Plusargs are command-line arguments passed to the simulator using the `+` prefix:

```bash
./simv +iterations=1000 +sim_type="experimental"
```

Inside the testbench, these arguments can be queried using SystemVerilog system functions such as:

```systemverilog
$test$plusargs(string)
$value$plusargs(user_string, variable)
```

## Reference Material

**LRM**

- [IEEE 1800-2023 - 21.6 Command Line Input](https://ieeexplore.ieee.org/document/10458102)
