---
icon: material/database-refresh-outline
---

# UVM Register Abstraction Layer (RAL)

## Register Model structure

The UVM register model is built from a small set of core abstractions:
**register fields**, **registers**, **memories**, **register blocks**, and
**register maps**. Together, these elements provide a structured 
representation of the design address space and allow register accesses to be
described independently from the physical bus
 protocol used to perform them.

A **register field** represents a group of bits associated with a specific function
inside a register. Each field has a defined width, a bit offset within the
register, and an access policy, such as read-write, read-only, or write-only. 

A **register** is then composed of one or more fields and models a complete hardware
register.

A **register block** represents a hardware block in the design. It contains the
registers and memories associated with that block, as well as one or more
register maps. Memories are modeled using `uvm_mem`. A memory has a size,
access policy, and address offset within a register map. Unlike registers, a
`uvm_mem` does not contain fields; accesses are performed using the full data
width of the memory.

A **register map** defines how registers and memories are placed in an address space
from the point of view of a specific bus interface. The same group of registers
may be accessible through different bus interfaces, each with its own address
offsets. This can be modeled by defining multiple address maps inside the same
parent register block.

The register map also connects the abstract register model to the bus-level
verification environment. It specifies which bus sequencer is used to perform
register accesses and which adapter converts generic UVM register transactions
into bus-specific sequence items, and vice versa.

## Register Model Data Types

The register model defines specific data types to provide a consistent
representation of address and data field widths.

| Type             | Default width | Define                      | Description                                                           |
| ---------------- | ------------- | --------------------------- | --------------------------------------------------------------------- |
| `uvm_reg_data_t` | 64 bits       | ``` `UVM_REG_DATA_WIDTH ``` | Used for register data fields (`uvm_reg`, `uvm_reg_field`, `uvm_mem`) |
| `uvm_reg_addr_t` | 64 bits       | ``` `UVM_REG_ADDR_WIDTH ``` | Used for register address variables                                   |

## Building blocks

The UVM Register Abstraction Layer, commonly referred to as RAL, represents the
register and memory structure of a design using a hierarchy of reusable classes.
This hierarchy allows registers, fields, memories, blocks, and address maps to
be modeled in a consistent way, independently of the bus protocol used to access
them. The main building blocks of a RAL model are summarized in the table
below.

| RAL element    | UVM class       | Description                                                                                                                       |
| -------------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Register field | `uvm_reg_field` | Models one or more bits inside a register, including width, bit offset, access policy, and reset behavior.                        |
| Register       | `uvm_reg`       | Models a complete hardware register and contains one or more register fields.                                                     |
| Memory         | `uvm_mem`       | Models an addressable memory region. Unlike registers, memories do not contain fields and are accessed using the full data width. |
| Register block | `uvm_reg_block` | Groups registers, memories, and maps that belong to the same hardware block or subsystem.                                         |
| Register map   | `uvm_reg_map`   | Defines how registers and memories are placed in an address space for a specific bus interface.                                   |


## Register Fields

The lowest-level element in the register model is the field, which represents
one or more bits inside a register. Each field is created as an instance of the
`uvm_reg_field` class. Fields belong to a `uvm_reg` object and are first
constructed, then configured using the `configure()` method.


```systemverilog
// uvm_reg_field
function void configure(
    uvm_reg        parent,   // The containing register 
    int unsigned   size,     // How many bits wide
    int unsigned   lsb_pos,  // Bit offset within the register
    string         access,   // "RW", "RO", "WO" etc
    bit            volatile, // Volatile if bit is updated by hardware
    uvm_reg_data_t reset,    // The reset value
    bit            has_reset,// Whether the bit is reset
    bit            is_rand,  // Whether the bit can be randomized
    bit            individually_accessible // i.e Totally contained within a byte lane
); 
```

| Argument                  | Meaning                                                                                                                                                                                                       |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `parent`                  | The `uvm_reg` object that contains this field. Usually you pass `this` from inside the register class.                                                                                                        |
| `size`                    | Width of the field in bits.                                                                                                                                                                                   |
| `lsb_pos`                 | Position of the least significant bit of the field inside the register.                                                                                                                                       |
| `access`                  | Access policy, such as `"RW"`, `"RO"`, `"WO"`, `"W1C"`, etc. [List of access policies](https://verificationacademy.com/verification-methodology-reference/uvm/docs_1.2/html/files/reg/uvm_reg_field-svh.html) |
| `volatile`                | Indicates that the field may be updated by hardware without a RAL write.                                                                                                                                      |
| `reset`                   | Reset value of the field.                                                                                                                                                                                     |
| `has_reset`               | Tells the model whether this field actually has a defined reset value.                                                                                                                                        |
| `is_rand`                 | Enables or disables randomization of the field value.                                                                                                                                                         |
| `individually_accessible` | Indicates whether the field is completely contained in an independently accessible byte lane.                                                                                                                 |


## Registers

Registers are modeled by extending the`uvm_reg` class. A register acts as a container 
for one or more `uvm_reg_field` objects, where each field represents a specific group of 
bits within the register. The general properties of the register, such as its name, width, and
supported coverage model, are defined in the class constructor through the
`new()` method.


```systemverilog
// uvm_reg
function new ( 
    string name = "",    // Register name 
    int unsigned n_bits, // Register width in bits 
    int has_coverage     // Coverage model supported by the register
);
```

| Argument       | Meaning                                                                                                                                                                                                                  |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`         | Name of the register object in the RAL model.                                                                                                                                                                            |
| `n_bits`       | Width of the register in bits.                                                                                                                                                                                           |
| `has_coverage` | Selects which register coverage models are enabled. See `uvm_coverage_model_e` in [`uvm_reg_model.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg_model.svh) |

A register class usually includes a `build()` method where its fields are created
and configured. This method is responsible for instantiating each `uvm_reg_field`,
assigning its bit width and position, defining its access policy, and setting
its reset value and other attributes. It is important to note that this `build()`
method is not part of the standard UVM build phase, because registers are
derived from `uvm_object` rather than `uvm_component`. Therefore, the register
`build()` method must be called explicitly when constructing the register model.

```systemverilog
class tdc_reg extends uvm_reg;
    `uvm_object_utils(tdc_reg)

    rand uvm_reg_field ctrl1;
    rand uvm_reg_field adj1;
    rand uvm_reg_field pxon;
    rand uvm_reg_field feon;

    // Function: new
    function new(string name = "tdc_reg");
        super.new(name, 8, UVM_NO_COVERAGE);
    endfunction : new

    // Function build
    virtual function void build();
        this.ctrl1 = uvm_reg_field::type_id::create("ctrl1");
        this.ctrl1.configure(this, 4, 0, "RW", 0, 'hf, 1, 1, 0);
        this.adj1 = uvm_reg_field::type_id::create("adj1");
        this.adj1.configure(this, 2, 4, "RW", 0, 'h1, 1, 1, 0);
        this.pxon = uvm_reg_field::type_id::create("pxon");
        this.pxon.configure(this, 1, 6, "RW", 0, 'h0, 1, 1, 0);
        this.feon = uvm_reg_field::type_id::create("feon");
        this.feon.configure(this, 1, 7, "RW", 0, 'h0, 1, 1, 0);
    endfunction : build

endclass : tdc_reg
```

When a register is included in a register block, the
register object is first created, its fields are built and configured, and then
the register itself is configured with its parent block. After this, the
register is added to one or more register maps, where its address offset and
access properties are defined for a specific bus interface.

```systemverilog
// uvm_reg
function void configure (
    uvm_reg_block blk_parent,           // Containing register block
    uvm_reg_file regfile_parent = null, // Optional register file parent 
    string hdl_path = ""                // Optional HDL path
);
```

| Argument         | Meaning                                                                                |
| ---------------- | -------------------------------------------------------------------------------------- |
| `blk_parent`     | The `uvm_reg_block` that contains this register.                                       |
| `regfile_parent` | Optional parent register file, if the model uses `uvm_reg_file`. Often left as `null`. |
| `hdl_path`       | Optional HDL path for backdoor access.                                                 |

## Memories

Memories are modeled by extending the`uvm_mem` class. A memory object 
represents an addressable memory region in the
design, rather than a collection of individual register fields. Unlike
registers, the UVM register model does not store the contents of every memory
location by default, since doing so could require a large amount of workstation
memory for large address spaces.

The main properties of a memory model are defined in the `new()`
constructor. These properties include the memory name, address range, data
width, access policy, and optional coverage model.

```systemverilog
// uvm_mem
function new (
  string           name,                          // Name of the memory model
  longint unsigned size,                          // Number of memory locations
  int unsigned     n_bits,                        // Width of each memory location
  string           access       = "RW",           // Access policy: "RW" or "RO"
  int              has_coverage = UVM_NO_COVERAGE // Functional coverage model
);
```

| Argument       | Description                                                                                                     |
| -------------- | --------------------------------------------------------------------------------------------------------------- |
| `name`         | Name of the memory model.                                                                                       |
| `size`         | Number of addressable locations in the memory.                                                                  |
| `n_bits`       | Width, in bits, of each memory location.                                                                        |
| `access`       | Access policy of the memory from the point of view of the register model. Common values are `"RW"` and `"RO"`.  |
| `has_coverage` | Built-in functional coverage model supported by the memory. Use `UVM_NO_COVERAGE` when coverage is not enabled. |

A simple memory model can be implemented as follows:

```systemverilog
// Memory array 1 - Size 32'h2000
class mem_1_model extends uvm_mem;

  `uvm_object_utils(mem_1_model)

  function new(string name = "mem_1_model");
    super.new(name, 32'h2000, 32, "RW", UVM_NO_COVERAGE);
  endfunction : new

endclass : mem_1_model
```

In this example, `mem_1_model` represents a read-write memory region with
`32'h2000` addressable locations, where each location is 32 bits wide. The model
does not enable built-in functional coverage.

## Register Maps

A register map defines how registers and memories are placed in an address
space. Its main role is to assign address offsets to the registers, memories, or
sub-blocks contained in a register block. In addition, a register map is later
used during testbench integration to connect register-based accesses to the
appropriate bus sequencer and adapter.

Registers and memories are added to a map using the `add_reg()` and `add_mem()`
methods. These methods associate a register or memory object with an address
offset and an access policy within a specific map.

```systemverilog
// uvm_reg_map
function void add_reg (
  uvm_reg           rg,
  uvm_reg_addr_t    offset,
  string            rights    = "RW",
  bit               unmapped  = 0,
  uvm_reg_frontdoor frontdoor = null
);
```

```systemverilog
// uvm_reg_map
function void add_mem (
  uvm_mem           mem,
  uvm_reg_addr_t    offset,
  string            rights    = "RW",
  bit               unmapped  = 0,
  uvm_reg_frontdoor frontdoor = null
);
```

| Argument     | Description                                                                                                                     |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| `rg` / `mem` | Handle to the register or memory object being added to the map.                                                                 |
| `offset`     | Address offset of the register or memory within the map.                                                                        |
| `rights`     | Access policy used for this map entry, such as `"RW"` or `"RO"`.                                                                |
| `unmapped`   | If set to `1`, the register or memory does not occupy a normal address in the map and requires a user-defined frontdoor access. |
| `frontdoor`  | Optional handle to a custom frontdoor access object used to access the register or memory.                                      |

A register block can contain more than one register map. This is useful when the
same block can be accessed through different bus interfaces or when different
address views are required. Each map can define its own address offsets and can
later be connected to a different target bus agent in the UVM environment.

## Register Blocks

The next level in the UVM register hierarchy is the `uvm_reg_block`. A register
block is used to group registers, memories, and address maps that belong to a
common hardware block. At a low level, it can represent the register space of a
single functional block. At a higher level, it can also be used to organize
multiple sub-blocks, allowing the register model to represent larger subsystems
or even a complete SoC.

A register block usually contains one or more address maps derived from
`uvm_reg_map`. These maps define where registers and memories appear in the
address space. A map is created inside the register block using the
`create_map()` method.

```systemverilog
// uvm_reg_block
function uvm_reg_map create_map (
  string           name,
  uvm_reg_addr_t   base_addr,
  int unsigned     n_bytes,
  uvm_endianness_e endian,
  bit              byte_addressing = 1
);
```


| Argument          | Description                                                                                                |
| ----------------- | ---------------------------------------------------------------------------------------------------------- |
| `name`            | Name of the register map.                                                                                  |
| `base_addr`       | Base address of the map.                                                                                   |
| `n_bytes`         | Access width of the associated bus, expressed in bytes.                                                    |
| `endian`          | Endianness used by the map, such as `UVM_LITTLE_ENDIAN`.                                                   |
| `byte_addressing` | Defines whether consecutive bus accesses increment by bytes or by address units. The default value is `1`. |

For example:

```systemverilog
// Insde class definition
uvm_reg_map APB_map;

// Inside build()
AHB_map = create_map("AHB_map", 'h0, 4, UVM_LITTLE_ENDIAN);
```

The `n_bytes` argument represents the bus word size. If a register is wider than
the bus width, UVM may need more than one bus transaction to complete the
register access. In that case, the `byte_addressing` argument controls how the
address is incremented between consecutive accesses.

For example, consider a 64-bit register located at offset `0`, accessed through
a map with `n_bytes = 4`. Since the bus width is 4 bytes, two bus accesses are
required. If `byte_addressing = 0`, the accesses occur at addresses `0` and `1`.
If `byte_addressing = 1`, the accesses occur at addresses `0` and `4`.

The first map created inside a register block is commonly assigned to the
block’s `default_map` handle. This default map is then used to add registers and
memories to the block address space.

```systemverilog
// Addrmap - mattonella_reg_block
class mattonella_reg_block extends uvm_reg_block;
  `uvm_object_utils(mattonella_reg_block)

  rand tdc_reg SET_TDC_DCO1_00;
  rand tdc_reg SET_TDC_DCO1_01;
  rand tdc_reg SET_TDC_DCO1_02;

  // Function: new
  function new(string name = "mattonella_reg_block");
    super.new(name, UVM_NO_COVERAGE);
  endfunction : new

  // Function: build
  virtual function void build();

    this.default_map = create_map("default_map", 0, 1, UVM_NO_ENDIAN);

    this.SET_TDC_DCO1_00 = tdc_reg::type_id::create("SET_TDC_DCO1_00");
    this.SET_TDC_DCO1_00.configure(this);
    this.SET_TDC_DCO1_00.build();
    this.default_map.add_reg(this.SET_TDC_DCO1_00, 'h0);

    this.SET_TDC_DCO1_01 = tdc_reg::type_id::create("SET_TDC_DCO1_01");
    this.SET_TDC_DCO1_01.configure(this);
    this.SET_TDC_DCO1_01.build();
    this.default_map.add_reg(this.SET_TDC_DCO1_01, 'h1);

    this.SET_TDC_DCO1_02 = tdc_reg::type_id::create("SET_TDC_DCO1_02");
    this.SET_TDC_DCO1_02.configure(this);
    this.SET_TDC_DCO1_02.build();
    this.default_map.add_reg(this.SET_TDC_DCO1_02, 'h2);

  endfunction : build

endclass : mattonella_reg_block
```

In this example, the register block creates a default address map with a base
address of `0` and an access width of one byte. Three instances of `tdc_reg` are
then created, configured with the current block as their parent, built to create
their fields, and finally added to the map at offsets `0`, `1`, and `2`.

### Using `default_map` or a Named Map

Every `uvm_reg_block` already provides a `default_map` handle. For simple register models with only one address map, it is common to assign the map returned by `create_map()` directly to `default_map`.

```systemverilog
this.default_map = create_map("default_map", 0, 1, UVM_NO_ENDIAN);
```

This approach is clean when the block has a single configuration interface and all registers are accessed through the same address space. Registers can then be added directly to `default_map`:

```systemverilog
this.default_map.add_reg(this.SET_TDC_DCO1_00, 'h0, "RW");
```

For larger designs, or for blocks with multiple bus interfaces, it can be useful to declare explicit map handles with more descriptive names. For example:

```systemverilog
uvm_reg_map cfg_map;
uvm_reg_map debug_map;
```

The named map can still be assigned to `default_map` if it should be used as the primary map:

```systemverilog
this.cfg_map = create_map("cfg_map", 0, 1, UVM_NO_ENDIAN);
this.default_map = this.cfg_map;
```

This style is helpful when the register block contains more than one address
view, such as a configuration map, a debug map, or maps associated with
different bus agents. In small blocks with only one address map, however, using
`default_map` directly is usually sufficient and avoids unnecessary
declarations.

### Endianness 

The `endian` argument of `create_map()` defines how multi-byte register values are split and ordered across bus accesses. This becomes relevant when the register width is larger than the bus access width defined by `n_bytes`.

```systemverilog
this.default_map = create_map("default_map", 0, 1, UVM_NO_ENDIAN);
```

Common values are:

| Value               | Description                                                              |
| ------------------- | ------------------------------------------------------------------------ |
| `UVM_LITTLE_ENDIAN` | The least significant byte is placed at the lowest address.              |
| `UVM_BIG_ENDIAN`    | The most significant byte is placed at the lowest address.               |
| `UVM_LITTLE_FIFO`   | Little-endian ordering for FIFO-style accesses.                          |
| `UVM_BIG_FIFO`      | Big-endian ordering for FIFO-style accesses.                             |
| `UVM_NO_ENDIAN`     | No byte reordering is applied or endianness is not relevant for the map. |

For example, consider a 32-bit register with value `32'hAABBCCDD` accessed through an 8-bit bus. With little-endian ordering, the bytes are accessed from least significant to most significant:

```text
Address offset + 0 -> DD
Address offset + 1 -> CC
Address offset + 2 -> BB
Address offset + 3 -> AA
```

With big-endian ordering, the most significant byte is accessed first:

```text
Address offset + 0 -> AA
Address offset + 1 -> BB
Address offset + 2 -> CC
Address offset + 3 -> DD
```

In many simple register models, especially when each register access uses the
full register width or when the bus interface is byte-oriented and no reordering
is needed, `UVM_NO_ENDIAN` may be sufficient. However, when registers are wider
than the bus access width, the selected endianness determines how the register
value is broken into individual bus transactions.

## Complete example

```systemverilog
// This file was autogenerated by PeakRDL-uvm
package ral_regs_pkg;
    `include "uvm_macros.svh"
    import uvm_pkg::*;

    typedef class mattonella_reg_block;
    
    // Reg - tdc_reg
    class tdc_reg extends uvm_reg;
        `uvm_object_utils(tdc_reg)

        rand uvm_reg_field ctrl1;
        rand uvm_reg_field adj1;
        rand uvm_reg_field pxon;
        rand uvm_reg_field feon;

        mattonella_reg_block reg_block;

        // Function: coverage
        covergroup cg_vals;
            option.per_instance = 1;
            ctrl1 : coverpoint ctrl1.value[3:0];
            adj1 : coverpoint adj1.value[1:0];
            pxon : coverpoint pxon.value[0:0];
            feon : coverpoint feon.value[0:0];
        endgroup : cg_vals

        // Function: new
        function new(string name = "tdc_reg");
            super.new(name, 8, build_coverage(UVM_CVR_FIELD_VALS));
            add_coverage(build_coverage(UVM_CVR_FIELD_VALS));
        	if (has_coverage(UVM_CVR_FIELD_VALS)) begin
                cg_vals = new();
                cg_vals.set_inst_name(name);
            end
        endfunction : new

        // Function: post_write
        virtual task post_write(uvm_reg_item rw);
            super.post_write(rw);
            if (rw.status == UVM_IS_OK && rw.map != null) begin
                uvm_reg_addr_t offset = get_address(rw.map);
                reg_block.sample_map_values(offset, 0, rw.map);
                this.sample_values();
                `uvm_info(get_type_name(), $sformatf("POST_WRITE"), UVM_DEBUG)
            end
        endtask : post_write

        // Function: post_read
        virtual task post_read(uvm_reg_item rw);
            super.post_read(rw);
            if (rw.status == UVM_IS_OK && rw.map != null) begin
                uvm_reg_addr_t offset = get_address(rw.map);
                reg_block.sample_map_values(offset, 1, rw.map);
                this.sample_values();
                `uvm_info(get_type_name(), $sformatf("POST_READ"), UVM_DEBUG)
            end
        endtask : post_read

        // Function: sample_values
        virtual function void sample_values();
           super.sample_values();
           if (get_coverage(UVM_CVR_FIELD_VALS)) begin
               cg_vals.sample();
           end
        endfunction : sample_values

        // Function build
        virtual function void build();
            if(!$cast(reg_block, get_parent())) begin
                `uvm_fatal("CAST_ERROR", "Cannot get parent reg_block")
            end
            this.ctrl1 = uvm_reg_field::type_id::create("ctrl1");
            this.ctrl1.configure(this, 4, 0, "RW", 0, 'hf, 1, 1, 0);
            this.adj1 = uvm_reg_field::type_id::create("adj1");
            this.adj1.configure(this, 2, 4, "RW", 0, 'h1, 1, 1, 0);
            this.pxon = uvm_reg_field::type_id::create("pxon");
            this.pxon.configure(this, 1, 6, "RW", 0, 'h0, 1, 1, 0);
            this.feon = uvm_reg_field::type_id::create("feon");
            this.feon.configure(this, 1, 7, "RW", 0, 'h0, 1, 1, 0);
        endfunction : build

    endclass : tdc_reg

    // Map Coverage Object
    class mattonella_reg_block_default_map_coverage extends uvm_object;
        `uvm_object_utils(mattonella_reg_block_default_map_coverage)

        covergroup ra_cov(string name) with function sample(uvm_reg_addr_t addr, bit is_read);

            option.per_instance = 1;
            option.name = name;

            ADDR: coverpoint addr {
                bins SET_TDC_DCO1_00 = { 'h0 };
                bins SET_TDC_DCO1_01 = { 'h1 };
                bins SET_TDC_DCO1_02 = { 'h2 };
            }

            RW: coverpoint is_read {
                bins RD = {1};
                bins WR = {0};
            }

            ACCESS: cross ADDR, RW {
            }

        endgroup: ra_cov

        // Function: new
        function new(string name = "mattonella_reg_block_default_map_coverage");
            ra_cov = new(name);
        endfunction : new

        // Function: sample
        function void sample(uvm_reg_addr_t offset, bit is_read);
            ra_cov.sample(offset, is_read);
        endfunction: sample

    endclass : mattonella_reg_block_default_map_coverage

    // Addrmap - mattonella_reg_block
    class mattonella_reg_block extends uvm_reg_block;
        `uvm_object_utils(mattonella_reg_block)

        rand tdc_reg SET_TDC_DCO1_00;
        rand tdc_reg SET_TDC_DCO1_01;
        rand tdc_reg SET_TDC_DCO1_02;

        mattonella_reg_block_default_map_coverage default_map_cg;

        // Function: new
        function new(string name = "mattonella_reg_block");
            super.new(name, build_coverage(UVM_CVR_ALL));
        endfunction : new

        // Function: build
        virtual function void build();

            if(has_coverage(UVM_CVR_ADDR_MAP)) begin
                default_map_cg = mattonella_reg_block_default_map_coverage::type_id::create("default_map_cg");
                default_map_cg.ra_cov.set_inst_name(this.get_full_name());
                void'(set_coverage(UVM_CVR_ADDR_MAP));
            end

            this.default_map = create_map("default_map", 0, 1, UVM_NO_ENDIAN);
            
            this.SET_TDC_DCO1_00 = tdc_reg::type_id::create("SET_TDC_DCO1_00");
            this.SET_TDC_DCO1_00.configure(this);
            this.SET_TDC_DCO1_00.build();
            this.default_map.add_reg(this.SET_TDC_DCO1_00, 'h0);
                
            this.SET_TDC_DCO1_01 = tdc_reg::type_id::create("SET_TDC_DCO1_01");
            this.SET_TDC_DCO1_01.configure(this);
            this.SET_TDC_DCO1_01.build();
            this.default_map.add_reg(this.SET_TDC_DCO1_01, 'h1);
                
            this.SET_TDC_DCO1_02 = tdc_reg::type_id::create("SET_TDC_DCO1_02");
            this.SET_TDC_DCO1_02.configure(this);
            this.SET_TDC_DCO1_02.build();
            this.default_map.add_reg(this.SET_TDC_DCO1_02, 'h2);
              
        endfunction : build

        // Function: sample_map_values
        function void sample_map_values(uvm_reg_addr_t offset, bit is_read, uvm_reg_map map);
           if(get_coverage(UVM_CVR_ADDR_MAP)) begin
              if(map.get_name() == "default_map") begin
                 default_map_cg.sample(offset, is_read);
                 `uvm_info(get_type_name(), $sformatf("SAMPLE_MAP_VALUES: %3d %3d", offset, is_read), UVM_DEBUG)
              end
           end
        endfunction: sample_map_values

    endclass : mattonella_reg_block

endpackage: ral_regs_pkg
```

## Functional Coverage

Functional coverage can be added to a UVM register model to track how the
register space is exercised during simulation. In this example, two types of
coverage are collected: field-value coverage for each register and address-map
coverage for the register block.

The field-value coverage is implemented inside the `tdc_reg` class. Each
register contains a covergroup that samples the mirrored values of its fields:

```systemverilog
covergroup cg_vals;
  option.per_instance = 1;
  ctrl1 : coverpoint ctrl1.value[3:0];
  adj1  : coverpoint adj1.value[1:0];
  pxon  : coverpoint pxon.value[0:0];
  feon  : coverpoint feon.value[0:0];
endgroup : cg_vals
```

The `option.per_instance = 1` setting keeps coverage separate for each instance
of `tdc_reg`. This is useful when multiple registers share the same register
type but appear at different addresses in the register map.

The covergroup is created only when field-value coverage is enabled:

```systemverilog
if (has_coverage(UVM_CVR_FIELD_VALS)) begin
  cg_vals = new();
  cg_vals.set_inst_name(name);
end
```

The register overrides the `post_write()` and `post_read()` callbacks to sample
coverage after successful RAL accesses. After a write or read completes, the
register obtains its address in the map, samples the map-level coverage through
the parent block, and then samples its own field-value coverage.

```systemverilog
virtual task post_write(uvm_reg_item rw);
  super.post_write(rw);
  if (rw.status == UVM_IS_OK && rw.map != null) begin
    uvm_reg_addr_t offset = get_address(rw.map);
    reg_block.sample_map_values(offset, 0, rw.map);
    this.sample_values();
  end
endtask : post_write
```

For reads, the same flow is used, but the access is marked as a read by passing `is_read = 1`.

The field-value covergroup is sampled through the `sample_values()` method. This
method overrides the corresponding method from `uvm_reg` and provides a single
sampling point for the custom field-value coverage.

```systemverilog
virtual function void sample_values();
  super.sample_values();

  if (get_coverage(UVM_CVR_FIELD_VALS)) begin
    cg_vals.sample();
  end
endfunction : sample_values
```

The call to `super.sample_values()` preserves the base class behavior. The
custom covergroup is sampled only when field-value coverage is currently
enabled, as checked by `get_coverage(UVM_CVR_FIELD_VALS)`. In this model,
`sample_values()` is called from the `post_write()` and `post_read()` callbacks,
so field values are sampled after each successful register access.

To allow the register callbacks to also trigger address-map coverage, each
`tdc_reg` stores a handle to its parent register block:

```systemverilog
mattonella_reg_block reg_block;
```

This handle is assigned during the register `build()` method by casting the
generic parent returned by `get_parent()` to the design-specific register block
type:

```systemverilog
if (!$cast(reg_block, get_parent())) begin
  `uvm_fatal("CAST_ERROR", "Cannot get parent reg_block")
end
```

This connection is needed because the map-level sampling method,
`sample_map_values()`, is implemented in the parent `mattonella_reg_block`.
Therefore, after a successful read or write, the register can call the parent
block to sample the address-map coverage and then sample its own field-value
coverage. This mechanism assumes that the register has already been configured
with its parent block before its `build()` method is called:

```systemverilog
this.SET_TDC_DCO1_00.configure(this);
this.SET_TDC_DCO1_00.build();
```

The call to `configure(this)` assigns the current register block as the parent
of the register. The subsequent call to `build()` can then recover that parent
using `get_parent()`.

Address-map coverage is implemented in a separate coverage object associated
with the register block. This covergroup samples the register address and
whether the access was a read or a write:

```systemverilog
covergroup ra_cov(string name) with function sample(uvm_reg_addr_t addr, bit is_read);

  option.per_instance = 1;
  option.name = name;

  ADDR: coverpoint addr {
    bins SET_TDC_DCO1_00 = { 'h0 };
    bins SET_TDC_DCO1_01 = { 'h1 };
    bins SET_TDC_DCO1_02 = { 'h2 };
  }

  RW: coverpoint is_read {
    bins RD = {1};
    bins WR = {0};
  }

  ACCESS: cross ADDR, RW {
  }

endgroup : ra_cov
```

The `ADDR` coverpoint tracks which register addresses were accessed. The `RW`
coverpoint tracks whether the access was a read or a write. The `ACCESS` cross
ensures that each register address is exercised with both access types.

The register block creates this coverage object when address-map coverage is
supported and then enables the address-map coverage model:

```systemverilog
if (has_coverage(UVM_CVR_ADDR_MAP)) begin
  default_map_cg = mattonella_reg_block_default_map_coverage::type_id::create("default_map_cg");
  default_map_cg.ra_cov.set_inst_name(this.get_full_name());
  void'(set_coverage(UVM_CVR_ADDR_MAP));
end
```

Here, `has_coverage()` checks whether the register block was constructed with
support for address-map coverage, while `set_coverage()` enables that coverage
model for collection.

The method `sample_map_values()` is used as the sampling entry point for map coverage. It checks that address-map coverage is enabled and that the access came from the expected map before sampling the coverage object.

```systemverilog
function void sample_map_values(uvm_reg_addr_t offset, bit is_read, uvm_reg_map map);
  if (get_coverage(UVM_CVR_ADDR_MAP)) begin
    if (map.get_name() == "default_map") begin
      default_map_cg.sample(offset, is_read);
    end
  end
endfunction : sample_map_values
```

Together, these coverage models answer two complementary questions. The
field-value coverage checks which values were observed in the fields of each
register, while the address-map coverage checks whether each register address
was accessed through both read and write operations. Both coverage models are
sampled from the same RAL read or write event, keeping the register-value and
address-map coverage synchronized.

## Reference Material

**Accellera**

- [UVM 1.2 Class Reference Index](https://verificationacademy.com/verification-methodology-reference/uvm/docs_1.2/html/index.html)

**Source Code**

- **Basic structure**

    - [Source code `uvm_reg_field.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg_field.svh)
    - [Source code `uvm_reg.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg.svh)
    - [Source code `uvm_mem.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_mem.svh)
    - [Source code `uvm_reg_block.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg_block.svh)
    - [Source code `uvm_reg_map.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg_map.svh)

- **Types**

    - [Source code `uvm_reg_model.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg_model.svh)
    - [Souce code `uvm_reg_sequence.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg_sequence.svh)

- **Adapter and predictor**

    - [Source code `uvm_reg_adapter.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg_adapter.svh)
    - [Source code `uvm_reg_predictor.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/reg/uvm_reg_predictor.svh)