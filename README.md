# VLSI System Design: SoC Design and Planning Workshop

## Day 1: Inception of Open-Source EDA, OpenLANE, Sky130 PDK

### How to talk to computers

#### 1. Introduction to QFN-48 package, chip, pads, core, die, IPs

We will discuss a few terms before diving into the SoC design details.

A typical **electronic board** consists of an electronic processor/chip and the interface to the outside world. The main components of an **electronic processor/chip** include: Package (example QFN-48), Die with pads, Wire bonds.T

**Foundry IPs (intellectual property)**: These are pre-designed and verified circuit blocks that can be integrated into larger chip designs. Examples include standard cells, memory blocks, analog circuits, etc.

**Macros**: These are large, complex blocks that serve specific, higher-level functions within a chip's architecture. Examples include processor cores, complext memories, DSP blocks, etc.

**Foundry Interface Files**: We communicate with foundries via foundry interface files which provide detaled guidelines and technical specifications necessary for designing ICs compatible with the foundry's manufacturing process. 

**Instruction set architecture**: ISA serves as an interface between software and hardware, providing a set of instructions that a CPU can execute. Typically software is converted to a sequence of hex and binary commands that can be executed in a layout to get an output/result.

**RTL (Register Transfer Level)** is the level at which a hardware designer specifies: 
- how each instruction in the ISA is executed within the hardware, 
- how data moves through the processor’s subsystems, and 
- how logical operations are synchronized and controlled.

It is described using a hardware description language (HDL). 

#### 2. Introduction to RISC-V

RISC-V (pronounced "risk-five") is an open standard for computer processor architectures. It's based on the RISC (Reduced Instruction Set Computing) principles, which emphasize simplicity and efficiency in the set of instructions that the processors can execute.

#### 3. From Software Applications to Hardware

**System software** (OS + compiler + assembler) converts apps to binary/assembly language. An example of this is a simple C/C++ program which is converted by a **compiler** to instructions, which are then converted to binary using an **assembler**.

### SoC design using OpenLANE

#### 1. Introduction to all components of open-source digital asic design

The process of **ASIC design** usually involves RTL designs, EDA tools, and PDK data.

**ASIC design flow** is a piece of software that takes the design from the RTL to the GDSII level. 

**PDK** is the **process design kit**. It is a collection of files used to model a fabrication process for EDA tools used for IC design. Google and Skywater open sourced a 130 nm production PDK in 2020. A 130 nm node is still sufficiently fast (example Intel P4EE @3.46 GHz).

#### 2. Simplified RTL2GDS flow

**RTL to GDSII** flow consists of several steps: Synthesis, Floor/Power planning, Plancement, Clock Tree Synthesis, Routing, and Sign off.

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

#### 1. OpenLANE Directory structure in detail 

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

**Synthesis** in the context of IC design refers to the process of converting a high-level design description (typically written in a hardware description language like Verilog or VHDL) into a gate-level netlist. The synthesis step can be run using the following command in OpenLane:

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

This report provides statistical information about the synthesis process, detailing various aspects of the synthesized design. 

> [!IMPORTANT]
> **The flop ratio can be calculated using the ratio of the number of D flip flops to the total number of cells = 1613/14876 = 10.8429%**

![image](https://github.com/joshisaumil/vsd-soc-design-and-planning/assets/10101904/7bf8bff8-d712-4e4e-9d7d-a9f064e194df)


## Day 2: Good floorplan vs bad floorplan and introduction to library cells

### Chip Floor planning considerations

#### 1. Utilization factor and Aspect ratio

Utilization factor = Area occupied by netlist / Total area of the core

Aspect Ratio = Height / Width

Example:

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image.png)

#### 2. Concept of pre-placed cells

Certain critical cells or blocks are positioned on the chip layout early in the design process before the automatic placement and routing steps, and are referred to as pre-placed cells. Their arrangement in the chip is referred to as floor planning.

#### 3. De-coupling capacitors

De-coupling capacitors are used to stabilize the power supply voltage within an integrated circuit (IC). These capacitors help filter out noise and provide a steady voltage level to ensure the proper functioning of the IC.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-1.png)

#### 4. Power planning

Power delivery is planned in a mesh design format to avoid issues like ground bouce and voltage droop.

Ground bounce is a phenomenon in VLSI (Very Large Scale Integration) design and other high-speed digital circuits where fluctuations in the ground voltage level occur due to switching activities of the transistors. This can cause significant problems in the proper functioning of the integrated circuits.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-2.png)


#### 5. Pin placement and logical cell placement blockage

Pin placement and cell placement/routing needs a handshake between frontend and backend. Then the area around the pins is blocked using blockage so that automated placement and routing tools don't place cells in the area.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-3.png)

#### 6. Run floorplan on OpenLane

To run the floorplanning process, type the following command in the openlane console.

```bash
% run_floorplan
```

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-4.png)

The resulting floorplan can be viewed from the results folder.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-5.png)

The file contains general information about the design, such as the design name, DEF version, and units used. It defines the boundary of the chip, includes information about the placement of components, pins, nets, special nets, and regions, all of which are essential for subsequent design stages. 

In the example below, the area of the chip is 660.685 $\mu m$ x 671.405 $\mu m$.  

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-6.png)

To view the layout, we'll start magic.

```bash
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def picorv32a.floorplan.def &
```

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-7.png)

Command reference:
- a: select visible
- s: select
- v: view
- z: zoom

### Library Binding and Placement

Place the components on the die.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-8.png)

Estimate wire lengths and capacitances, add buffers/repeaters when needed.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-9.png)

Run the placement command on OpenLane. The placement step will reduce the HPWL (half parameter wire length) and converge the overflow parameter.

```bash
% run_placement
```

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-10.png)

View the resulting layout in magic.

```bash
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def picorv32a.floorplan.def &
```

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-11.png)

### Cell design and characterization flows

Library has cells with different functionality, different sizes, different threshold voltages, etc.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-13.png)

Cell design flow consists of inputs, the design steps, and the outputs.

The inputs include PDKs, DRC/LVS rules, SPICE models, library, and user defined specs.

Design step includes circuit design, layout design (Euler's path + stick diagram), and characterization.
- The output of circuit design is a CDL (circuit description language) file.
- The output of layout design is a GDSII file, LEF file, and the extracted SPICE netlist.
- The output of the characterization step is the timing, noise, and power .lib files.

### General timing characterization parameters

Following are some of the parameters we need for timing characterization
- slew_low_rise_threshold
- slew_high_rise_threshold
- slew_high_fall_threshold
- slew_high_fall_threshold
- in_rise_threshold
- in_fall_threshold
- out_rise_threshold
- out_fall_threshold

Propagation delay is calculated using the above parameters. It is calculated as time(out\_\*\_thr) - time(in\_\*\_thr)

Examples:

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-14.png)



## Day 3: Design library cell using magic layout and ngspice characterization

### SPICE modeling

A SPICE model is created using components, their values, and the nodes around them.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-16.png)

### CMOS Inverter

An inverter is a fundamental digital logic gate that performs the logical NOT operation. In digital electronics, an inverter takes a single binary input and produces an output that is the opposite (complement) of the input. 

The switching threshold of an inverter, often referred to as the inverter threshold voltage, is a critical parameter in digital circuit design. 

It is the input voltage at which the output of the inverter switches states, meaning it is the point where the inverter's output changes from high (logic 1) to low (logic 0), or vice versa. This threshold is typically defined as the input voltage level where the output voltage equals the input voltage.

```bash
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-17.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-18.png)

View the mag file in magic.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-19.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-21.png)

### CMOS fabrication process

CMOS fabrication process involves multiple steps. 

Create active regions

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-22.png)

N-well and P-well formation

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-23.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-24.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-25.png)

Formation of the gate terminal

The threshold voltage of a transistor is a function of the doping concentration and the oxide capacitance

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-26.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-27.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-28.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-29.png)

Lightly doped drain formation

We need LDD to lower the hot electron effect and the short channel effects.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-31.png)

Source and Dain formation

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-32.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-33.png)

Contacts and Interconnects

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-34.png)

Higher Level Metal Formation

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-35.png)

### CMOS Inverter Layout

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-36.png)

### SPICE parameters and netlist

Extract SPICE parameters

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-39.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-40.png)



```bash
* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=0.01u
.include ./libs/nshort.lib
.include ./libs/pshort.lib
.subckt sky130_inv A Y VPWR VGND
M1001 Y A VGND VGND nshort_model.0 w=35 l=23
+ ad=1435 pd=152 as=1365  ps=148
M1000 Y A VPWR VPWR pshort_model.0 w=37 l=23
+ ad=1443 pd=152 as=1517 ps=156
VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
C0 A Y 0.05fF
C1 Y VPWR 0.11fF
C2 A VPWR 0.07fF
C3 Y 0 0.24fF
C4 VPWR 0 0.59fF

.tran 1n 20n
.control
run
.endc
.end
```

Run the script on ngspice.

```bash
ngspice sky130_inv.spice
```

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-41.png)

### CMOS inverter characterization

To calculate the rise time and fall time, we use 20% and 80% of the max voltage.

20% of 3.3V = 0.66V
80% of 3.3v = 2.64V

Rise time = (2.20369 - 2.16147) ns = 0.04222 ns

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-42.png)

Fall time = (4.06813 - 4.0401) ns = 0.02803 ns

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-43.png)

To calculate the cell rise and fall delay, we use 50% of the max voltage.

50% of 3.3V = 1.65 V

Cell Rise Delay = (2.18456 - 2.15) ns = 0.03456 ns

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-44.png)

Call Fall Delay = (4.05432 - 4.05) ns = 0.00432 ns

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-45.png)

### Introduction to Magic tool options and DRC rules

Magic VLSI documentation: http://opencircuitdesign.com/magic/

Skywater PDK documentation: https://skywater-pdk.readthedocs.io/en/main/

Downloading DRC tech test files.
![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-46.png)

Viewing the .magicrc file

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-47.png)

met3 periphery rules are listed in the skywater periphery rules page: https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html#m3

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-48.png)

Viewing the rule violation for met3

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-49.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-50.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-51.png)

Viewing another rule violation for metal3

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-52.png)

Load the poly file

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-53.png)

Find the incorrect structure. See that the rule violation is not identified.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-54.png)

The distance between the poly and npolyres is only 0.21 $\mu m$.
![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-56.png)

The rule, however, is > 0.48 $\mu m$.
![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-57.png)

Edit the relevant location of the file with the appropriate rule.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-59.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-60.png)

Reload the tech file and check the drc again.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-62.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-63.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-64.png)

Identifying other simple incorrect DRC.

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-66.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-65.png)

Adding the relevant rule

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-67.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-68.png)

Lab challenge to find missing or incorrect rules and fix them

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-69.png)


![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-71.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-70.png)

![alt text](https://github.com/joshisaumil/vsd-soc-design-and-planning/blob/main/assets/images/image-72.png)

