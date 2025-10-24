---
draft: false
date: 2025-01-05
description: >
  UVM Configuration Database
authors: Ciro Bermudez
icon: material/database-search
hide: 
#  - navigation
#  - toc
---

# :material-database-search: UVM Configuration Database

## Summary

<div class="justify" markdown>
The `uvm_config_db` is a UVM utility class that is used to pass configuration data
objects between component objects in a UVM testbench.
</div>

## Methods

<div class="justify" markdown>
To understand better how to use the `get()` and `set()` methods, let's first take a look at
their definitions.
</div>

### set

```sv linenums="1"
static function void set(
  uvm_component cntxt,
  string        inst_name,
  string        field_name,
  T             value
);
```

#### Description

<div class="justify" markdown>
Creates or updates a configuration setting in the UVM configuration database.
This method allows you to store configuration values that can be retrieved by
components during the build phase or runtime.
</div>

| Parameter    | Type            | Description                                                                                               |
| ------------ | --------------- | --------------------------------------------------------------------------------------------------------- |
| `cntxt`      | `uvm_component` | The context component from which the configuration scope is determined. Can be `null` for absolute paths. |
| `inst_name`  | `string`        | The instance path, either relative to `cntxt` or absolute when `cntxt` is `null`.                         |
| `field_name` | `string`        | The name of the configuration field to set.                                                               |
| `value`      | `T`             | The value to assign to the configuration field. Type `T` is parameterized.                                |

#### Scope Resolution

The full scope of the configuration setting is determined by the combination of `cntxt` and `inst_name`:

##### With Context `(cntxt != null)`

When a context component is provided, the full scope is constructed as:

```plain linenums="1"
<cntxt_path>.inst_name.field_name
```

Example:

```sv linenums="1"
uvm_config_db#(int)::set(this, "m_agent.m_driver", "num_transactions", 100);
// If 'this' is at path "m_env", the full scope becomes:
// m_env.m_agent.m_driver.num_transactions
```

##### Without Context `(cntxt == null)`

When `cntxt` is `null`, `inst_name` must provide the complete hierarchical path:

```plain linenums="1"
inst_name.field_name
```

Example:

```sv linenums="1"
uvm_config_db#(int)::set(null, "uvm_test_top.m_env.m_agent.m_driver", "num_transactions", 100);
// Full scope: uvm_test_top.m_env.m_agent.m_driver.num_transactions
```

#### Notes

- The `set()` method is typically called during the build phase or in the testbench top module
- Wildcards (`*`) can be used in `inst_name` for pattern matching
- Settings must be made before the components retrieve them using `get`
- Later `set()` calls to the same scope will override earlier values

### `get`

```sv linenums="1"
static function bit get(
  uvm_component cntxt,
  string        inst_name,
  string        field_name,
  inout T       value
);
```

#### Description

<div class="justify" markdown>
Retrieves a configuration value from the UVM configuration database.
This method searches for a configuration setting that matches the
specified scope and field name, starting from the given context component.
Returns 1 (true) if the value is found, 0 (false) otherwise.
</div>

| Parameter    | Type            | Description                                                                                                          |
| ------------ | --------------- | -------------------------------------------------------------------------------------------------------------------- |
| `cntxt`      | `uvm_component` | The starting point for the configuration search. Typically `this` in the calling component.                          |
| `inst_name`  | `string`        | The instance path relative to `cntxt`. Can be an empty string `""` if the configuration applies directly to `cntxt`. |
| `field_name` | `string`        | The name of the configuration field to retrieve.                                                                     |
| `value`      | `inout T`       | Reference parameter that receives the retrieved value if found. Type T is parameterized.                             |

#### Notes

## Real examples

### 1. Connecting a UVM testbench to a DUT using the `uvm_config_db`

From `tb.sv` we declare an interface called `rst_if()` and then we push into de `uvm_config_db` the
interface

```sv linenums="1" title="tb.sv"
gpio_uvc_if rst_if ();
// Push virtual interface down to the GPIO UVC reset agent
initial begin
  uvm_config_db#(virtual gpio_uvc_if)::set(null, "uvm_test_top.m_env.m_rst_agent", "vif", rst_if);
  run_test();
end
```

```sv linenums="1" title="gpio_uvc_config.sv"
class gpio_uvc_config extends uvm_object;
  ...
  virtual gpio_uvc_if     vif;
  uvm_active_passive_enum is_active = UVM_ACTIVE;
  ...
endclass : gpio_uvc_config
```

```sv linenums="1" title="gpio_uvc_agent.sv"
class gpio_uvc_agent extends uvm_agent;
  ...
  gpio_uvc_config    m_config;
  gpio_uvc_sequencer m_sequencer;
  gpio_uvc_driver    m_driver;
  gpio_uvc_monitor   m_monitor;
  ...
endfunction : connect_phase

function void gpio_uvc_agent::connect_phase(uvm_phase phase);
  if (m_config.vif == null) begin
    `uvm_fatal(get_name(), "gpio_uvc virtual interface is not set!")
  end
  
  m_monitor.vif         = m_config.vif;
  m_monitor.m_config    = m_config;
  m_monitor.analysis_port.connect(this.analysis_port);
 
  if (m_config.is_active == UVM_ACTIVE) begin
    m_driver.seq_item_port.connect(m_sequencer.seq_item_export);
    m_driver.vif         = m_config.vif;
    m_driver.m_config    = m_config;
    m_sequencer.m_config = m_config;
  end
endfunction : connect_phase
```

```sv linenums="1" title="top_env.sv"
class top_env extends uvm_env;
  ...
  gpio_uvc_config m_rst_config;
  gpio_uvc_agent  m_rst_agent;
  ...
endclass : top_env

function void top_env::build_phase(uvm_phase phase);
  // Create configuration object for the GPIO UVC reset
  m_rst_config = gpio_uvc_config::type_id::create("m_rst_config");
  m_rst_config.is_active = UVM_ACTIVE;
  
  // Retrieve virtual interface handle from uvm_config_db
  if (!uvm_config_db #(virtual gpio_uvc_if)::get(this, "m_rst_agent", "vif", m_rst_config.vif)) begin
    `uvm_fatal(get_name(), "Could not retrieve gpio_uvc_if from config db")
  end

  // Push configuration object down to the GPIO UVC reset agent
  uvm_config_db#(gpio_uvc_config)::set(this, "m_rst_agent", "config", m_rst_config);
  m_rst_agent = gpio_uvc_agent::type_id::create("m_rst_agent", this);
...
endfunction : build_phase
```

- The `uvm_config_db::get()` method is a function that returns a bit value to indicate whether the object retrieval was been successful or not; this is tested to ensure that the testbench does not proceed if the lookup fails.

## Coding Guidelines

<div class="justify" markdown>

!!! tip "Tip"

    - No tip
    ```verilog
    var_name = transaction_type::type_id::create("var_name");
    ```

</div>

## Reference Material

**Accellera**

- [UVM 1.2 Class Reference `uvm_config_db`](https://verificationacademy.com/verification-methodology-reference/uvm/docs_1.2/html/files/base/uvm_config_db-svh.html)
- [UVM 1.2 Class Reference Index](https://verificationacademy.com/verification-methodology-reference/uvm/docs_1.2/html/index.html)

**Verification Methodology Cookbooks**

- [UVM Cookbook pdf](https://verificationacademy.com/resource/128026c9-49b3-3eb8-92a4-08373425cd36)
- [UVM Configuration Database](https://verificationacademy.com/cookbook/uvm-universal-verification-methodology/the-uvm-configuration-database-uvm-config_db/)

**Source Code**

- [Source code `uvm_config_db.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/base/uvm_config_db.svh)