# VLSI System Design: SoC Design and Planning Workshop

## Day 1: Inception of Open-Source EDA, OpenLANE, Sky130 PDK

### How to talk to computers

#### 1. Introduction to QFN-48 package, chip, pads, core, die, IPs

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

#### 2. Introduction to RISC-V

RISC-V (pronounced "risk-five") is an open standard for computer processor architectures. It's based on the RISC (Reduced Instruction Set Computing) principles, which emphasize simplicity and efficiency in the set of instructions that the processors can execute.

Components of a typical **RISC-V SoC (System on Chip)** die include:
- CPU core
- Memory management unit
- Interrupt controller
- Timers/Clock Management circuits
- On chip memory
- Peripherals (GPIO/UART/SPI/USB)
- Other components

#### 3. From Software Applications to Hardware

**System software** (OS + compiler + assembler) converts apps to binary/assembly language. An example of this is a simple C/C++ program which is converted by a **compiler** to instructions, which are then converted to binary using an **assembler**.

### SoC design using OpenLANE

#### 1. Introduction to all components of open-source digital asic design

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

#### 2. Simplified RTL2GDS flow

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

#### 3. Introduction to OpenLANE and striVe chipsets

**Efabless** created an open source ASIC implementation methodology and flow called OpenLANE. Its public repository is on github [here](https://github.com/The-OpenROAD-Project/OpenLane). The goal of openlane is to produce a clean (no LVS/DRC/Timing violations) GDSII with no human intervention. It can be used to generate GDSII for macros and chips. 

**striVe** is a family of open source SoCs.

#### 4. Introduction to OpenLANE detailed ASIC design flow

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

### Get familiar with open-source EDA tools

#### 1. OpenLANE Directory structure in detail (Work in Progress)

The openlane working directory has 2 main folders: 
- openlane
- pdks

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/888579e2-252c-4bc7-be40-8c01baec4c6a)

The pdks folder has the skywater pdk and other open source pdks.

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/36d866ea-5fa6-4602-8fd0-57ba193c466a)

The skywater pdk has 2 main folders: libs.tech and libs.ref

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/493e0545-0b27-4582-a47a-2935105c76d1)

libs.tech contains files related to all the tools used in the design flow.

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/44ab208f-214f-4897-9906-99c7fb460b05)

libs.ref contains the standard cell files. We will be using "sky130_fd_sc_hd" which is the "SKY130 High Density Digital Standard Cells" library.

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/668bf51f-8ba2-44ae-bba7-b48bd09db2bc)

This folder contains the technology files.

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/ca776c10-66d4-4181-bb8e-4a2c7dfa005e)

Openlane can be launced as follows.

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/25f4ae33-1254-47bd-b272-802fdf8cf213)

#### 2. Design Preparation Step

We will use the **picorv32a** for our design. The **PicoRV32** is a CPU core based on the RISC-V architecture. It is well-known for being a small, highly configurable RISC-V compatible processor core implemented in Verilog. The PicoRV32 core is designed to be simple and small, making it ideal for integration into Field Programmable Gate Arrays (FPGAs) and other digital integrated circuits where space and power efficiency are important.

A **.lef** file, which stands for "Library Exchange Format," is a type of file used in integrated circuit (IC) design. Cell LEF and Technology LEF are two types of LEF files:

**Cell LEF** files are used to describe the physical characteristics of cells in a library. These descriptions include information such as the size of the cell (height and width), the placement of pins (inputs and outputs), and other physical attributes necessary for layout purposes.

**Technology LEF** files provide details about the different layers of the IC, such as metal layers, via layers, and other technological layers used in the manufacturing process. This information is crucial for ensuring that the design complies with the specific process technology of a semiconductor foundry.

LEF files can contain routing rules and constraints that specify how connections can be made between different components on the chip. This includes the widths and spacings of wires, via sizes, and other parameters that are important for ensuring reliable manufacturing and operation.

The preparation step merges the **cell level .lef** file and the **technology level .lef** file. 

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/b7633d18-ff8e-4065-97cb-5870ef86791f)

#### 3. Review files after design preparation and run synthesis

After the preparation step is complete, files are generated in the runs folder.

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/e5c1ddd0-8986-4542-a2a5-f3c4c226ba5e)

The merged LEF file is also created. A merged LEF file is a combination of Technology LEF and Cell LEF files into a single file, which simplifies the management and usage of these files in the IC design process. 

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/3045cc14-ab94-46e8-8d2c-f001a7e75935)

By merging these files, a designer can streamline their design workflow, ensuring that all necessary data for both the technology specifications and the individual cell designs are available in one document.

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/63fccc8b-acbe-4df9-ad3a-a8115c85c934)

#### 4. OpenLANE Project Git Link Description

The git project is here: [https://github.com/The-OpenROAD-Project/OpenLane](https://github.com/The-OpenROAD-Project/OpenLane)

#### 5. Steps to characterize synthesis results

**Synthesis** in the context of IC design refers to the process of converting a high-level design description (typically written in a hardware description language like Verilog or VHDL) into a gate-level netlist. 

The synthesis step can be run using the following command in OpenLane:

```bash
% run_synthesis
```

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/6e9c7bb5-4eda-464e-864e-8e7f36f73f0a)

Once the synthesis is successful, we can view the reports and the results.

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/8cd9407f-691a-4101-85df-3f448dff1789)

The results folder has the picorv32a.synthesis.v file. This is the Verilog output of the synthesis process for the picorv32a design. 

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/a0e125f3-a66e-4f53-9ea8-e3cc397c7eec)

The reports folder has the statistics report 1_yosys_4.stat.rpt

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/6faed9c1-a7d1-4ccd-aeec-6409b08aec1e)

This report provides statistical information about the synthesis process, detailing various aspects of the synthesized design. (flop ratio calculated = 1613/14876 = 10.8429%)

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/7bf8bff8-d712-4e4e-9d7d-a9f064e194df)
