**# Digital Filling Station**

**# Automated Conveyor Filling Station**

## 1.0 Project Overview

This repository contains the software assets and Functional Design Specification (FDS) for an automated conveyor filling station. The objective is to detect arriving containers, identify their colored labels, dispense the correct product (pecans or walnuts), and dispatch the filled containers using discrete logic execution.

**Target Hardware/Environment:** RSLogix 500 Micro Starter Lite - free license                                                                                                                                                                                                                                                                                       
**Programming Language:** Ladder Diagram (LD)

## 2.0 System Architecture & Process Flow

The physical architecture consists of a primary conveyor motor, two product hoppers (Pecan and Walnut) controlled via solenoids, a proximity sensor for box detection, two optical photo eyes for color detection, and a level switch for fill status.

*Please refer to the process diagram:* `Process_Diagram_Filling_Station.png`

## 3.0 Control Philosophy

The control system operates under the following automated sequence:

1. **Continuous Operation:** The conveyor motor operates continuously to feed boxes into the filling station.
2. **Arrival & Stoppage:** Upon box arrival, the proximity switch actuates. The conveyor motor interlocks and stops to align the box with the hoppers.
3. **Product Sorting (Optical Detection):** 
    * If the RED photo eye actuates, the Pecan hopper solenoid energizes.
    * If the BLUE photo eye actuates, the Walnut hopper solenoid energizes.
4. **Dispatch:** Once the box reaches maximum capacity, the level switch actuates. The active hopper instantly de-energizes, and the conveyor motor re-energizes to dispatch the box.
5. **Safety Interlock:** Neither hopper can energize if the proximity switch is open (no box present), preventing product spillage.

## 4.0 I/O Allocation Schedule

| Tag / Address | I/O Type | Device Description | Field State Definition |
| :--- | :--- | :--- | :--- |
| `I:0/0` | Digital Input  | ZS-01: Proximity Switch | Closed = Box Present |
| `I:0/1` | Digital Input  | LSH-01: Level Switch | Closed = Box Full |
| `I:0/2` | Digital Input  | PE-01: Red Photo Eye | Closed = Red Label Detected |
| `I:0/3` | Digital Input  | PE-02: Blue Photo Eye | Closed = Blue Label Detected |
| `O:0/0` | Digital Output | M-01: Conveyor Motor | 1 = Energized / Running |
| `O:0/1` | Digital Output | XV-01: Walnut Hopper Solenoid | 1 = Open / Dispensing |
| `O:0/2` | Digital Output | XV-02: Pecan Hopper Solenoid | 1 = Open / Dispensing |

## 5.0 Simulation & Verification Protocol (Dry Run Test)

The logic was validated via RSLogix Emulate software. The system successfully passed the following Dry Run Test simulation states:

| Test State | Condition / Input Action | Expected Output | Status |
| :--- | :--- | :--- | :---: |
| **1. Startup** | Initial power-up. <br> All inputs (0) | Conveyor `O:0/0` **ENERGIZES** <br> Hoppers **OFF** | ✅ PASS |
| **2. Box Arrival** | Proximity switch actuates. <br> `I:0/0` (1) | Conveyor `O:0/0` **DE-ENERGIZES** <br> Hoppers **REMAIN OFF** | ✅ PASS |
| **3. Pecan Fill Cycle** | Prox & Red PE actuated. <br> `I:0/0` (1), `I:0/2` (1) | Pecan Hopper `O:0/2` **ENERGIZES** <br> Conveyor & Walnut **OFF** | ✅ PASS |
| **4. Walnut Fill Cycle** | Prox & Blue PE actuated. <br> `I:0/0` (1), `I:0/2` (0), `I:0/3` (1) | Walnut Hopper `O:0/1` **ENERGIZES** <br> Conveyor & Pecan **OFF** | ✅ PASS |
| **5. Fill Complete / Dispatch** | Level switch actuates. <br> `I:0/1` (1) added to State 4 | Conveyor `O:0/0` **RE-ENERGIZES** <br> Hoppers **DE-ENERGIZE** | ✅ PASS |
| **6. Continuous Run** | Box departs station. <br> All inputs (0) | Conveyor `O:0/0` **REMAINS ENERGIZED** <br> Hoppers **REMAIN OFF** | ✅ PASS |
| **7. Safety Interlock** | Photo eyes actuated without Prox. <br> `I:0/0` (0), `I:0/1` (0), `I:0/2` (1), `I:0/3` (1) | Hoppers `O:0/1`, `O:0/2` **REMAIN OFF** <br> (No product release without box) | ✅ PASS |

## 6.0 Software Assets

* The raw ladder logic project file (`.RSS`) can be found in the `/src/` directory.
* A complete PDF export of the ladder logic program is available in the `/docs/` directory.

## 7.0 Acknowledgements & My Learning Journey

This project is a reflection of my ongoing, highly structured learning journey in PLC programming. The online courses I undertook made it incredibly easy to grasp the fundamentals and challenged me to apply critical thinking—specifically the 80/20 rule. By truly mastering just 20% of the core instruction sets, we can effectively execute 80% of real-world automation tasks. 

Special thanks to the course instructor for the exercise materials:
* **Course Detail:** Applied Logic (via Udemy) by Paul Lynn
* **Project Concept:** The base process flow, diagrams, and core test criteria were provided as part of his excellent course materials.