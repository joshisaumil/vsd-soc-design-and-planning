# VLSI System Design: SoC Design and Planning Workshop

## Day 1: Inception of Open-Source EDA, OpenLANE, Sky130 PDK

### SKY130_D1_SK1: How to talk to computers

#### 1. SKY_L1: Introduction to QFN-48 package, chip, pads, core, die, IPs

We will discuss a few terms before diving into the SoC design details.

A typical **electronic board** consists of an electronic processor/chip and the interface to the outside world.

The main components of an **electronic processor/chip** include:
- Package (example QFN-48)
- Die with pads
- Wire bonds

**Foundry IPs (intellectual property)**: These are pre-designed and verified circuit blocks that can be integrated into larger chip designs. Examples include standard cells, memory blocks, analog circuits, etc.

**Macros**: These are large, complex blocks that serve specific, higher-level functions within a chip's architecture. Examples include processor cores, complext memories, DSP blocks, etc.

**Foundry Interface Files**: We communicate with foundries via foundry interface files which provide detaled guidelines and technical specifications necessary for designing ICs compatible with the foundry's manufacturing process. There are several types of foundry interface files:
- Design Rule Manuals
- Process Design Kits
- SPICE models
- Parameteic test structures
- IP Cores
- Reliability data
- GDSII layer map

**Instruction set architecture**: ISA serves as an interface between software and hardware, providing a set of instructions that a CPU can execute. Typically software is converted to a sequence of hex and binary commands that can be executed in a layout to get an output/result.

**RTL (Register Transfer Level)** is the level at which a hardware designer specifies: 
- how each instruction in the ISA is executed within the hardware, 
- how data moves through the processor’s subsystems, and 
- how logical operations are synchronized and controlled.

It is described using a hardware description language (HDL). 

Example: To add 2 numbers, the ISA will specify details like the opcode for the addition instruction and how the operands are specified. The RTL implementation of this would detail:

- How the operands are fetched from the register file.
- How the addition is carried out (e.g., using an arithmetic logic unit, ALU).
- How the result is written back to a register.
- How signals like the clock and control flags are used to manage this process.

#### 2. SKY_L2: Introduction to RISC-V

RISC-V (pronounced "risk-five") is an open standard for computer processor architectures. It's based on the RISC (Reduced Instruction Set Computing) principles, which emphasize simplicity and efficiency in the set of instructions that the processors can execute.

Components of a typical **RISC-V SoC (System on Chip)** die include:
- CPU core
- Memory management unit
- Interrupt controller
- Timers/Clock Management circuits
- On chip memory
- Peripherals (GPIO/UART/SPI/USB)
- Other components

#### 3. SKY_L3: From Software Applications to Hardware

**System software** (OS + compiler + assembler) converts apps to binary/assembly language. An example of this is a simple C/C++ program which is converted by a **compiler** to instructions, which are then converted to binary using an **assembler**.

### SKY130_D1_SK2: SoC design using OpenLANE

#### 1. SKY_L1: Introduction to all components of open-source digital asic design

The process of **ASIC design** usually involves
- RTL designs
- EDA tools
- PDK data

**ASIC design flow** is a piece of software that takes the design from the RTL to the GDSII level. 

**PDK** is the **process design kit**. It is a collection of files used to model a fabrication process for EDA tools used for IC design.

One analogy to PDKs is me wanting to construct a new building. To start building, I need specific tools and materials that fit with the local building regulations and the types of buildings that are commonly constructed in the area. A PDK is similar to that toolkit and materials guide for chip designers. The PDK ensures that engineers design chips in a way the fab can actually build them. It consists of:

- Process design rules 
- Device models
- Digital standard cell libraries
- I/O libraries

Google and Skywater open sourced a 130 nm production PDK in 2020. A 130 nm node is still sufficiently fast (example Intel P4EE @3.46 GHz).

#### 2. SKY_L2: Simplified RTL2GDS flow

**RTL to GDSII** flow consists of several steps:
1. Synthesis
2. Floor/Power planning 
3. Plancement
4. Clock Tree Synthesis
5. Routing
6. Sign off

**Synthesis**: converts the RTL to a circuit made out of components from the standard cell library. The resultant circuit is called the gate level netlist. These standard cells have regular layout. 

**Floor/Power planning**: plan silicon area and create robust power distribution network. Power planning helps reduce power loss and IR drop.

**Placement**: gate level netlist cells are placed on floorplan rows. The placement is done in 2 steps: global (may not be legal) and detailed (legal).

**Clock tree synthesis**: Clock distribution network is created to deliver the clock to all sequential elements. Goal is to minimize the skew and latency in the delivered clock signal. The clock distribution network usually looks like a tree.

**Routing**: This step implements the interconnect using the available metal layers. Global routing and detailed routing are used.

**Sign-off**: This step involves physical verifications (Design rule checking and Layout vs. Schematic checks) and timing verification (static timing analysis) to make sure timing constraints are met.

#### 3. SKY_L3: Introduction to OpenLANE and striVe chipsets

**Efabless** created an open source ASIC implementation methodology and flow called OpenLANE. Its public repository is on github [here](https://github.com/The-OpenROAD-Project/OpenLane). The goal of openlane is to produce a clean (no LVS/DRC/Timing violations) GDSII with no human intervention. It can be used to generate GDSII for macros and chips. 

**striVe** is a family of open source SoCs.

#### 4. SKY_L4: Introduction to OpenLANE detailed ASIC design flow

It is based on many open source projects that perform:

1. RTL synthesis (Yosys + abc)
2. STA (OpenSTA)
3. DFT (Fault)
4. Floorplanning/Placement/CTS/Optimization/Antenna rule violations/Global routing (OpenROAD)
5. Design Exploration
6. LEC (Yosys)
7. Detailed routing (Triton Route)
8. RC extraction
9. STA (OpenSTA)
10. Physical verification (magic + netgen)
11. gds2 streaming (magic)

### SKY130_D1_SK3: Get familiar with open-source EDA tools

#### 1. SKY_L1: OpenLANE Directory structure in detail

Work in Progress

#### 2. SKY_L2: Design Preparation Step

Work in Progress

#### 3. SKY_L3: Review files after design preparation and run synthesis

Work in Progress

#### 4. SKY_L4: OpenLANE Projecct Git Link Description

Work in Progress

#### 5. SKY_L5: Steps to characterize synthesis results

Work in Progress
