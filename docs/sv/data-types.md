---
icon: material/shape
---

# Data types

SystemVerilog provides several integer data type:

## Two-state data types

| Data Type           | Signed (default) | Size    | Value Range                                                 | States | Default Value |
| ------------------- | ---------------- | ------- | ----------------------------------------------------------- | ------ | ------------- |
| `bit`               | No               | 1 bit   | `0` to `1`                                                  | 2      | `0`           |
| `byte`              | Yes              | 8 bits  | `-128` to `127`                                             | 2      | `0`           |
| `byte unsigned`     | No               | 8 bits  | `0` to `255`                                                | 2      | `0`           |
| `shortint`          | Yes              | 16 bits | `-32,768` to `32,767`                                       | 2      | `0`           |
| `shortint unsigned` | No               | 16 bits | `0` to `65,535`                                             | 2      | `0`           |
| `int`               | Yes              | 32 bits | `-2,147,483,648` to `2,147,483,647`                         | 2      | `0`           |
| `int unsigned`      | No               | 32 bits | `0` to `4,294,967,295`                                      | 2      | `0`           |
| `longint`           | Yes              | 64 bits | `-9,223,372,036,854,775,808` to `9,223,372,036,854,775,807` | 2      | `0`           |
| `longint unsigned`  | No               | 64 bits | `0` to `18,446,744,073,709,551,615`                         | 2      | `0`           |

## Four-state data types

| Data Type | Signed (default) | Size    | Value Range                                                                    | States | Default Value |
| --------- | ---------------- | ------- | ------------------------------------------------------------------------------ | ------ | ------------- |
| `logic`   | No               | 1 bit   | `0`, `1`, `X`, `Z`                                                             | 4      | `X`           |
| `reg`     | No               | 1 bit   | `0`, `1`, `X`, `Z`                                                             | 4      | `X`           |
| `integer` | Yes              | 32 bits | `-2,147,483,648` to `2,147,483,647`, but it can encode the `X`, and `Z` states | 4      | `X`           |

## Non-integer types

| Data Type   | Signed (default) | Size    | Value Range       | States     | Default Value |
| ----------- | ---------------- | ------- | ----------------- | ---------- | ------------- |
| `time`      | No               | 64 bits | `0` to `2^64 − 1` | 4          | `0`           |
| `shortreal` | Yes              | 32 bits | IEEE-754 float    | Continuous | `0.0`         |
| `real`      | Yes              | 64 bits | IEEE-754 double   | Continuous | `0.0`         |
| `realtime`  | Yes              | 64 bits | IEEE-754 double   | Continuous | `0.0`         |


## Reference Material

**LRM**

- [IEEE 1800-2023: 6. Data types](https://ieeexplore.ieee.org/document/10458102)

**Websites**

- [Doulos: SystemVerilog Data Types](https://www.doulos.com/knowhow/systemverilog/systemverilog-tutorials/systemverilog-data-types/)
