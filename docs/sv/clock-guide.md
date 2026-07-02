---
icon: material/clock
---

# Clock 

Before taking about the clock some important concepts are needed 
to understand how timing itself works inside SystemVerilog.

## The ``` `timescale``` compiler directive

The ``` `timescale``` compiler directive specifies the default time unit and
precision for all design elements that follow this directive and do not have `timeunit` and
`timeprecision` constructs specified within the design element. The ``` `timescale``` directive
remains in effect from when it is encountered in the source code until another ``` `timescale```
compiler directive is read.
The ``` `timescale``` directive only affects the current compilation units.

The following example specifies a time unit of 1ns with a precision of 1 ps, meaning 1000 point
per unit.

```systemverilog
`timescale 1ns / 1ps
```

Because the ``` `timescale``` directive remains in effect from the point where it is
encountered, the effective timescale can depend on the compilation order.
Therefore, if a source file does not define its own timescale, it may use the
timescale left by a previously compiled file, which can lead to unexpected
simulation behavior.

!!! warning "Warning"

    The ``` `timescale``` directive can result in file order dependency problems.

## The `timeunit` and `timeprecision` keywords

The time unit and precision can be declared by the `timeunit` and `timeprecision` keywords.
The time precision may also be declared using an optional second argument
to the `timeunit` keyword using the slash separator

===  "Separate"
    ```systemverilog
    module tb;
      timeunit      1ns;
      timeprecision 1ps;
    endmodule : tb
    ```

===  "Combined"
    ```systemverilog
    module tb;
      timeunit 1ns / 1ps;
    endmodule : tb
    ```

Defining the `timeunit` and `timeprecision` constructs within the design element
removes the file order dependency problems with compiler directives.

There shall be at most one time unit and one time precision for any module,
program, package, or interface definition or in any compilation-unit scope.
This shall define a time scope. If specified, the `timeunit` and
`timeprecision` declarations shall precede any other items in the current time scope. 

!!! tip "Tips"

    Prefer using `timeunit` and `timeprecision` over ``` `timescale ``` directive.

## Precedence of `timeunit`, `timeprecision` and ``` `timescale ```

If a timeunit or timeprecision is not explicitly declared inside a module,
program, package, or interface, SystemVerilog determines it using a precedence
order.

The order is:

1. **Nested scope inheritance**: If the module or interface is nested, it
    inherits the time unit/precision from the enclosing module or interface.
2. **Previous ``` `timescale ``` directive**: Otherwise if a previous ``` `timescale ```
    directive exists in the same compilation unit, the last one encounteres is used.
3. **Compilation-unit `timeunit` declaration**: Otherwise, if the compilation-unit scope
    defines a `timeunit`, that value is used.
4. **Implementation default**: Otherwise, the simulator's default time unit and precision
    are used.

## Simulation time unit

The global time precision, also called the simulation time unit, is the smallest
precision value specified anywhere in the design. It is computed from all
`timeprecision` declarations, the precision arguments of `timeunit` declarations,
and the precision arguments of all ``` `timescale ``` directives. The `1step` delay
corresponds to one unit of this global precision. Unlike physical time units
such as `ns`, `ps`, or `fs`, `step` cannot be used to define or modify a time unit or
time precision.

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

    The time precision of a design element shall be at least as precise as the
    time unit; it cannot be a longer unit of time than the time unit
    
    
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

## Reference Material

**LRM**

- [IEEE 1800-2023: 3.14 Simulation time units and precision](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 22.7 ``` `timescale```](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 20.3 Simulation time system functions](https://ieeexplore.ieee.org/document/10458102)
- [IEEE 1800-2023: 20.4  Timescale system task and system functions](https://ieeexplore.ieee.org/document/10458102)


