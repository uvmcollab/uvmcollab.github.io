---
icon: octicons/package-16
---

# UVM Sequence Item

## Summary

The **UVM Sequence Item** is the base class for user-defined transactions that leverage the stimulus generation and control capabilities of the sequence-sequencer mechanism.

## Coding Guidelines

This coding guidelines are directly taken form [Easier UVM Coding Guidelines](https://www.doulos.com/media/1277/easier-uvm-coding-guidelines-2016-06-24.pdf).
We encourage users to follow these recommendations so that the code becomes more readable and easier to maintain. By doing so, you will also benefit from better performance and simpler integration.

!!! tip "Tips"

    - Use conditional compilation guards to avoid compiling the same include file more than once.
    - Create user-defined transaction classes by extending the class `uvm_sequence_item`.
    - Try to minimize the number of distinct transaction classes
    - Register the transaction class with the factory using the macro ``uvm_object_utils` as the first line within the class. 
    - Do not use field macros. (This comes at a heavy cost in terms of performance)
    - After the factory registration macro, declare any member variables (using the prefix `m_` as a naming convention).
    - Use the `rand` qualifier in front of any class member variables that might need to be randomized, now or in the future.
    - After any member variables, define a constructor that includes a single string name argument with a default value of the empty string, a call to `super.new()`, and is otherwise empty.
    ```systemverilog
    function new (string name = "");
      super.new(name);
    endfunction : new
    ```
    - After the constructor, always override the `convert2string`, `do_copy`, `do_compare`, `do_print`, and `do_record` methods.
    - Always instantiate transaction objects using the factory.
    ```systemverilog
    var_name = transaction_type::type_id::create("var_name");
    ```
    - In general, the string name of the transaction should be the same as the variable name. 

## Code Example

``` systemverilog linenums="1" title="gpio_uvc_sequence_item.sv"
// TODO: add code later
```

## Reference Material

**Accellera**

- [UVM 1.2 Class Reference `uvm_sequence_item`](https://verificationacademy.com/verification-methodology-reference/uvm/docs_1.2/html/files/seq/uvm_sequence_item-svh.html)
- [UVM 1.2 Class Reference Index](https://verificationacademy.com/verification-methodology-reference/uvm/docs_1.2/html/index.html)

**Verification Methodology Cookbooks**

- [UVM Cookbook PDF](https://verificationacademy.com/resource/128026c9-49b3-3eb8-92a4-08373425cd36)
- [UVM Cookbook Webpage](https://verificationacademy.com/cookbook/uvm-universal-verification-methodology/)
- [UVM Sequences](https://verificationacademy.com/cookbook/uvm-universal-verification-methodology/uvm-sequences/)
- [Sequences](https://verificationacademy.com/cookbook/uvm-universal-verification-methodology/sequences/)
- [UVM Sequence Items](https://verificationacademy.com/cookbook/uvm-universal-verification-methodology/sequence-items/)
- [Transaction Methods](https://verificationacademy.com/cookbook/uvm-universal-verification-methodology/transaction-methods/)

**Source Code**

- [Source code `uvm_sequence_item.svh`](https://github.com/edaplayground/eda-playground/blob/master/docs/_static/uvm-1.2/src/seq/uvm_sequence_item.svh)

**Articles**

- [UVM Transactions - Definitions, Methods and Usage - Sunburst Design](https://www.paradigm-works.com/hubfs/49408364/technical-library/Sunburst/CummingsSNUG2014SV_UVM_Transactions_rev1_1.pdf)
