---
icon: fontawesome/solid/ruler-horizontal
---

# Verification Plan and Test Plan

## Introduction

In a digital verification project, one of the first documents that must be created is the **Verification Plan**. This document acts as a guide that connects the functional specification of the design with the verification activities used to prove that the design works correctly.

For students who are new to verification, it is very common to confuse the Verification Plan with the Test Plan. Although they are closely related, they are not the same, and understanding the difference early greatly simplifies the verification process.

This document explains both concepts using clear language, practical examples, and straightforward guidelines.

---

## What is Verification?

Verification is the process of answering a fundamental question:

> **Does the design do what the specification says it should do?**

Verification does not mean testing everything exhaustively. Instead, it means testing intelligently, focusing on the most important functionality and the most critical scenarios.

Even for relatively simple designs, it is usually impossible to verify everything due to time and resource constraints. For this reason, verification always involves prioritization.

---

# Verification Plan

## What is a Verification Plan?

The **Verification Plan** describes **what must be verified**, not how it will be done.

It is derived directly from the functional specification and answers questions such as:

- What features of the design must be verified?
- Under what conditions should those features be verified?
- Which features are critical and which are secondary?
- Which configuration combinations are important?

In other words, the Verification Plan defines the **scope of verification**.

---

## Simple Example

If the DUT is a FIFO, the Verification Plan might include:

- Basic read and write operations
- Full and empty conditions
- Reset behavior
- Overflow and underflow handling
- Different depth configurations

At this stage, the document does **not** describe how each item will be tested — it only states that it must be verified.

---

# Test Plan

## What is a Test Plan?

The **Test Plan** describes **how the verification will be performed**.

It defines the methodologies and techniques that will be used, for example:

- RTL simulation
- UVM-based testbenches
- Directed tests
- Constrained-random testing
- SystemVerilog Assertions
- Scoreboards
- Functional coverage
- Code coverage

---

## Relationship Between the Two Plans

- **Verification Plan → What to verify**
- **Test Plan → How to verify it**

Both documents must be consistent and aligned.

---

# Functional Coverage

## Why is Functional Coverage Needed?

Passing tests does not guarantee that the design has been thoroughly verified. Functional coverage answers another key question:

> **How much of the intended design behavior has actually been exercised?**

Functional coverage allows the verification team to measure progress and identify scenarios that have not yet been tested.

---

## Functional Coverage Development Process

Functional coverage is not created automatically. It requires:

- Interpreting the specification
- Understanding the design
- Deciding which values and scenarios are important

This process is iterative:

1. Analyze the available specifications
2. Identify features to be verified
3. Implement parts of the testbench
4. Add functional coverage
5. Run tests
6. Refine the coverage model

This cycle repeats until **coverage closure** is achieved.

---

# Testbench Architecture

A typical testbench has two main sides:

## Stimulus Side

- Generates transactions
- Controls the DUT
- Drives the DUT into different states

## Analysis Side

- Observes DUT behavior
- Checks correctness
- Implements scoreboards
- Collects functional coverage

> **Important rule:** Functional coverage must be based on what is observed, not on what is intended by the stimulus.

---

# Functional Coverage Models in SystemVerilog

SystemVerilog provides two primary approaches:

## 1. Covergroups (Data Coverage)

- Sample data values
- Define bins and cross coverage
- Useful for configurations, packet fields, and parameters

**Examples:**

- Packet types
- Address ranges
- Operating modes

---

## 2. Cover Properties (Temporal Coverage)

- Based on sequences and properties
- Measure behavior over time
- Ideal for protocols and control paths

**Examples:**

- A complete bus transaction
- FIFO reaching full or empty

---

# Important Guidelines

## Base Coverage on Requirements

Each coverage point should be traceable to a functional requirement.

---

## Observation-Based Coverage

Coverage should never be sampled from stimulus. It must be sampled from:

- Monitors
- Scoreboards
- Analysis transactions

This prevents false coverage.

---

## Coverage Validity

Functional coverage is only valid when the corresponding check passes.

- Failed tests should not contribute to coverage
- Verification tools usually manage this automatically

---

## Positive and Negative Tests

If a feature requires both:

- Valid behavior tests
- Invalid or error-condition tests

Coverage should be collected separately for each case.

---

## Appropriate Level of Abstraction

Not everything should be modeled with the same level of detail:

- Small fields → explicit values
- Large fields → ranges or categories

**Example:**

- 32-bit addresses → divide into meaningful ranges

---

## Important Values

Avoid automatic bins for wide fields. Instead:

- Identify key values
- Identify important ranges
- Ignore irrelevant values

---

## Dependencies Between Variables

If relationships exist between variables, use cross coverage to capture them.

---

## Boundary Conditions

Always include:

- Minimum values
- Maximum values
- Critical transitions
- Known corner cases

---

## Illegal Conditions

Define illegal bins to:

- Detect design bugs
- Detect testbench errors

Illegal bins do not contribute to coverage, but they improve verification quality.

---

## Correct Sampling Time

A coverage point should be sampled only when:

- Data is valid
- Data is stable
- The associated check has passed

---

## Temporal and Hybrid Coverage

In many cases, the best solution is to combine:

- Temporal properties to detect events
- Covergroups to analyze conditions

This approach is known as **hybrid coverage**.

---

# Final Summary

A good verification plan:

- Starts from the specification
- Prioritizes functionality
- Uses functional coverage effectively
- Evolves throughout the project

Always remember:

- **Verification Plan → What to verify**
- **Test Plan → How to verify it**

Both documents are essential tools for professional verification.
