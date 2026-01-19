---
icon: material/clock
---

# Clock SystemVerilog


## Simulation time units and precision

Simulation time models how long operations would take in real hardware.
In SystemVerilog, **all delays and time values are interpreted using two parameters**:

- **Time unit**: the unit of measurement (ns, ps, fs, …)

- **Time precision**: the rounding resolution applied to delays

Understanding these two concepts is essential to avoid rounding bugs, mismatched clocks, and file-order–dependent simulations.

**Time unit string**

| Character string | Unit of measurement |
| :--------------: | ------------------- |
|       `s`        | seconds             |
|       `ms`       | milliseconds        |
|       `us`       | microseconds        |
|       `ns`       | nanoseconds         |
|       `ps`       | picoseconds         |
|       `fs`       | femtoseconds        |

!!! tip "Tips"

    The time precision of a design element shall be at least as precise as the time unit; it cannot be a longer unit of time than the time unit
    

## Formatting time output

The `$timeformat` system task controls how `%t` displays time:

```systemverilog
$timeformat(-9, 3, " ns", 10);
```

**`$timeformat` default values for arguments**

| Argument            | Default                                                                                                  |
| ------------------- | -------------------------------------------------------------------------------------------------------- |
| units_number        | The smallest time precision argument of all the `timescale compiler directives in the source description |
| precision_number    | 0                                                                                                        |
| suffix_string       | A null character string                                                                                  |
| minimum_field_width | 20                                                                                                       |


**Time unit and precision number values**

| Value | Time unit or precision | Value | Time unit or precision |
| :---: | :--------------------: | :---: | ---------------------- |
|   2   |         100 s          |  -7   | 100 ns                 |
|   1   |          10 s          |  -8   | 10 ns                  |
|   0   |          1 s           |  -9   | 1 ns                   |
|  -1   |         100 ms         |  -10  | 100 ps                 |
|  -2   |         10 ms          |  -11  | 10 ps                  |
|  -3   |          1 ms          |  -12  | 1 ps                   |
|  -4   |         100 us         |  -13  | 100 fs                 |
|  -5   |         10 us          |  -14  | 10 fs                  |
|  -6   |          1 us          |  -15  | 1 fs                   |

## Overview


## Reference Material

**LRM**

- [IEEE 1800-2023: 3.14 Simulation time units and precision](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 22.7 ``` `timescale```](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 20.3 Simulation time system functions](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 20.4  Timescale system task and system functions](https://ieeexplore.ieee.org/document/10458102)
