# Physical Design with OpenLANE using Sky130 PDK

Notion notes link - https://satvik3799.notion.site/Physical-Design-using-OpenLANE-with-Sky130-PDK-dfa6e85766e14a88ab9e8596e87e422c


# Physical Design using OpenLANE with Sky130 PDK

Created: May 23, 2023 2:38 PM

## Inside a chip:

The chip is interfaced to the outside world using pads, from there the chip is accessed with different signals. 

The core is where the digital logic of the ASIC sits.

The Die is the size of the package or the skeleton for the IC.

The IC would generally consist an SoC, SRAM, PLLs (to divide or multiple the main clock frequency), ADC and DAC blocks in order to communicate with the outside world which is analog and couple of more blocks. The blocks are called **************************Foundry IPs (Foundry intellectual property).************************** 

Foundry is where the chips are manufactured and their research gives us the PDKs according to which the ICs are designed. The Foundry IPs are proprietary and since the research costs a lot of money, the PDKs are highly confidential; although there are open source PDKs such as Skywater130, which is a 130nm process node meaning the channel length between the source and drain is 130nm. Currently there are ICs which has 5nm process node. 

Macro is a purely digital logic that is designed using a Hardware Description language, for ex. the SoC shown in the figure is a Macro. 

<aside>
💡 Communication with the foundry is very important even for embedded designs, because for an SoC with same functionality having different process node could cause signal integrity issues for a PCB as rise time would be different for SoCs manufactured with different process node; so for the already tested and working PCB with the SoC manufactured with 130nm process node would cause signal integrity issues if the SoC is replaced with SoC manufactured with 7nm process node. Hence the decisions are to be made keeping in mind what the foundry offers.

</aside>

![Untitled](Physical%20Design%20using%20OpenLANE%20with%20Sky130%20PDK%20dfa6e85766e14a88ab9e8596e87e422c/Untitled.png)

![Untitled](Physical%20Design%20using%20OpenLANE%20with%20Sky130%20PDK%20dfa6e85766e14a88ab9e8596e87e422c/Untitled%201.png)

![Untitled](Physical%20Design%20using%20OpenLANE%20with%20Sky130%20PDK%20dfa6e85766e14a88ab9e8596e87e422c/Untitled%202.png)

## Instruction Set Architecture (ISA)

Instruction Set Architecture (ISA) is a set of rules and specifications that define the interface between the hardware and software of a computer system. It determines how a processor understands and executes instructions provided by software programs. It allows software developers to write programs without worrying about the specific details of the underlying hardware, as long as they adhere to the instructions and conventions defined by the ISA.

A code is written in easily understandable language by Humans, which is compiled into an Assembly code according to an ISA. The Assembly code is in Hexadecimal numbers which are executed as binary numbers or on/off of the transistors which are layed out in particular design which can run an the particular ISA for which the layout is designed for. The bits gets inside the layout and we get the output.

Taking an example of RISC-V ISA. The layout for the ISA is designed using an HDL language and then a RTL description of the same. The RTL description implements the RISC-V architecture specifications and the RTL to GDSII flow is followed to realize an IC that can run that code written by Humans.

![Untitled](Physical%20Design%20using%20OpenLANE%20with%20Sky130%20PDK%20dfa6e85766e14a88ab9e8596e87e422c/Untitled%203.png)

![Untitled](Physical%20Design%20using%20OpenLANE%20with%20Sky130%20PDK%20dfa6e85766e14a88ab9e8596e87e422c/Untitled%204.png)

## From Software application to Hardware:

The application softwares are read by the system software which consists of Operating system, Compiler and Assembler. The system software converts the application software into the binary code for the hardware. 

The OS takes the application software, compiles it and converts it into the assembly language. This is the major operation of the OS apart from allocating memory, Handling IO operations and low level system functions such as Memory management, Process management, User interface, Task scheduling, Power management, Error handling and logging, File system management, Networking etc. 

![Untitled](Physical%20Design%20using%20OpenLANE%20with%20Sky130%20PDK%20dfa6e85766e14a88ab9e8596e87e422c/Untitled%205.png)

The outputs of an OS are small functions in one of the languages such as C, C++, Java, VB etc. These small functions are then compiled to get the assembly language code. The syntax of the assembly code is dependent on the Hardware designed, for ex. Intel x86 hardware has different assembly code syntax, RISC-V has different syntax. These syntax are called ISA. The assembler converts the code in machine language, the binary language, which is fed to the hardware. The hardware gives output according the pattern of the binary numbers recieved.

![Untitled](Physical%20Design%20using%20OpenLANE%20with%20Sky130%20PDK%20dfa6e85766e14a88ab9e8596e87e422c/Untitled%206.png)

![Untitled](Physical%20Design%20using%20OpenLANE%20with%20Sky130%20PDK%20dfa6e85766e14a88ab9e8596e87e422c/Untitled%207.png)



# Labs

## Lab - 1 (Initiating Openlane, Design setup stage and synthesis)

## Initiating OpenLANE:

1.  Go to the OpenLane directory using “ cd ” command.
2. Initiate Docker using : “
    
    ```c
    docker
    ```
    
    The linux prompt would change to “ bash-4.2$ “
    
    ![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled.png)
    
3. To initiate the OpenLane: 
    
    ```c
    ./flow.tcl -interactive
    ```
    
    The command initiates a step by step interactive openlane flow. The prompt would change again:
    
    ![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%201.png)
    
    All the packages needed to run openlane are imported by the command: 
    
    ```c
    package require openlane 0.9
    ```
    

Now further steps to start the automated RTL to GSDII design flow can be performed.

Here, Picorv32a design is being explored. The design is already tweaked to its best output and the source files are already given in the designs folder. In the folder there are at least 3 files: src, config.tcl, and sky130A_sky130_fd_sc_hd_config.tcl

The src folder contains the verilog files and SDC “**Synopsys Design Constraint**” file. SDC is a common format for constraining the design which is supported by almost all Synthesis, PnR and other tools.

The config.tcl file can be edited to change or override the default settings done by openlane.

<aside>
💡 sky130A_sky130_fd_sc_hd can be interpreted as (pdk variation)_(pdk)_(foundry)_(standard cell)_(high density).
There can be other variations of PDK with acronyms:
hdll - 
ms - 
ls - 
hs -

</aside>

sky130A_sky130_fd_sc_hd_config.tcl file contains another design tweaks that can be done to modify the design.

The priority order is sky130A_sky130_fd_sc_hd_config.tcl > config.tcl > Default configurations.

How to decide priority in:

sky130A_sky130_fd_sc_hd_config.tcl

sky130A_sky130_fd_sc_hdll_config.tcl

sky130A_sky130_fd_sc_ms_config.tcl

sky130A_sky130_fd_sc_ls_config.tcl

sky130A_sky130_fd_sc_hs_config.tcl

### Design setup stage:

 We need to setup file system specific to the flow. Each step of the flow needs a location for the files to fetch, that location is created in the design preparation stage. It is done using command,: 

```c
prep -design picorv32a
```

The "prep" command is used to set up the necessary configurations, input files, and initial environment required for the subsequent stages of the OpenLane flow.

 "-design": This is an option or flag used with the "prep" command to specify the design to be prepared. In this case, "picorv32a" is the name of the design that is being specified. It indicates that the "prep" command will prepare the environment specifically for the "picorv32a" design.

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%202.png)

During this stage, cell level LEF and technology level LEF files are combined into a single file so that the openlane flow need not go to different files to fetch information of cell geometry, layers etc. Technology level LEF describes the overall process technology characteristics, while cell level LEF describes the physical and electrical properties of individual standard cells within a specific library or cell set. These two LEF files are merged into one called **merged.lef**.

<aside>
💡 LEF stands for "Library Exchange Format.” LEF files are used to describe the physical and electrical properties of library cells or standard cells. These files provide essential information for the physical design stages, such as placement and routing, within the electronic design automation (EDA) toolchain.

1. Cell Geometries: Defines shape and boundaries of standard cells, including height, width and layer information.
2. Pins and Ports: The pins and ports associated with each cell. These definitions include properties like name, direction (input or output), and layer information for metal connections.
3. Layers: Describe the different layers used in the design, such as metal layers, polysilicon, diffusion, etc. Each layer is defined with its name, type, direction, and other parameters, allowing the physical design tools to understand the available layers for routing and manufacturing purposes.
4. Site and Symmetry Information: Information about the site, which represents the placement area or grid that a cell occupies. They may include symmetry details to specify the symmetrical properties of the cells.
5. Electrical Information: Electrical properties of cells, including capacitance values, resistance values, and other electrical characteristics. These details help in estimating power consumption, delay calculations, and other electrical analysis during the design process.
</aside>

This step will generate a “ runs ” folder in the design folder of the picorv32a. Inside it there will be another folder named with the date this stage was performed on. 

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%203.png)

The “ results ” folder will have files generated when different steps such as floor planning, CTS, synthesis etc are performed. Similiarly for reports folder.

This file again has config.tcl shows what parameters have been modified. With every step performed there will be a config.tcl file generated, which will inform about what changes have been made.

The merged.lef file can be accessed in the tmp folder, by using the command: 

```c
less merged.lef
```

Quit by pressing “ q ”.

### Synthesis

The **yosys** and **ABC** tools are utilized to convert RTL to gate level netlist. After the design setup is ready, To run the synthesis and generate gate-level netlist by command:

```c
run_synthesis
```

In the report generated by synthesis, we are interested in chip module area, Flop ratio and buffer ratio as of now:

Flop ratio = no. of D-flip flops used/no. of cells used.

here, flop ratio = 1613/14876 = 0.1084

Buffer ratio ???

what do each of these words mean?? a2111o_2 and so on??

The statistics report of the synthesis is generated in the reports folder in the synthesis directory, and it is accessed using “less” command. The file is named “yosys_4.stat.rpt”

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%204.png)

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%205.png)

## Lab - 2 (Floor plan and Placement)

Two parameters are of importance when it comes to floor planning namely, Utilisation Factor and Aspect Ratio. They are defined as follows:

```
Utilisation Factor =  Area occupied by netlist
                     __________________________
                        Total area of core

```

```
Aspect Ratio =  Height
               ________
                Width

```

A Utilization Factor of 1 signifies 100% utilization leaving no space for extra cells such as buffer. However, practically, the Utilisation Factor is 0.5-0.6. Likewise, an Aspect ratio of 1 implies that the chip is square shaped. Any value other than 1 implies rectangular chip.

Priority order of configuration files to be used by the Openlane flow:

`sky130A_sky130_fd_sc_hd_config.tcl`

`conifg.tcl`

`floorplan.tcl` - System default variables.

The variables we are interested in as of now:

Floorplan environment variables or switches:

1. `FP_CORE_UTIL` - floorplan core utilization
2. `FP_ASPECT_RATIO` - floorplan aspect ratio
3. `FP_CORE_MARGIN` - Core to die margin area
4. `FP_IO_MODE` - defines pin configurations (1 = equidistant/0 = not equidistant)
5. `FP_CORE_VMETAL` - vertical metal layer
6. `FP_CORE_HMETAL` - horizontal metal layer

***Note: Vertical metal layer and Horizontal metal layer values will be 1 more than that specified in the files.***

After setting the desired variables, to run the picorv32a floorplan in openLANE:

```c
run_floorplan
```

After the floorplan run, a .def file will have been created within the `results/floorplan` directory. The system defaults will have been overriden by switches set in `conifg.tcl` and further overriden by switches set in `sky130A_sky130_fd_sc_hd_config.tcl`. We may review floorplan files by using the tool magic.

To view the floorplan, Magic is invoked after moving to the `results/floorplan` directory, where the flooprplan’s .def file is generated. We need to give location for tech lef file of the PDK, which is available in the `/openlane_working_dir/pdks/sky130A/libs.tech/magic` folder. Notice that the .tech file’s name is also included in the command, this is important or else the tool will not find the .tech file. The syntax for the command is:

```c
magic -T <path to .tech file> read lef <path to .lef file> read def <path to .def file>
```

The command used:

```c
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%206.png)

### Navigating in Magic:

1. Use “ s + v ” to fit the design into center and make everthing visible.
2. Use left click and the right click to make a box and then press “ z ” to zoom in.
3. Hold “s” to see the name of a cell after zooming in.
4. Use arrow keys to traverse.

Here, equidistant input pins (FP_IO_MODE = 1) can be viewed:

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%207.png)

Zoomed in views:

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%208.png)

The standard cell can be found at the bottom left corner:

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%209.png)

### Placement

The next step in the OpenLANE ASIC flow is placement. The synthesized netlist is the be placed on the floorplan. Placement is perfomed in 2 stages:

1. Global Placement: It finds optimal position for all cells which may not be legal and cells may overlap. Optimization is done through reduction of half parameter wire length
2. Detailed Placement: It alters the position of cells post global placement so as to legalise them

### Placement run on OpenLANE & view in Magic

Run using the command:

```
run_placement
```

The objective of placement is the convergence of overflow value. If overflow value reduces during the placement run it means that the design will converge and placement will be successful. The design can be viewed on magic within `results/placement` directory:

```
magic -T /home/aastha/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2010.png)

Zoomed-in views of the standard cell placement:

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2011.png)

![Untitled](Labs%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2012.png)

### Standard Cell Design Flow

Standard cell design flow steps are:

1. Inputs: PDKs, DRC & LVS rules, SPICE models, libraries, user-defined specifications
2. Design steps: Circuit design, Layout design, Extraction of parasitics, Characterization (timing, noise, power)
3. Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib files

### Standard Cell Characterization Flow

A typical standard cell characterization flow includes the following steps:

1. Read in the models and tech files
2. Read extracted spice netlist
3. Recognize behavior of the cell
4. Read the subcircuits
5. Attach power sources
6. Apply stimulus to characterization setup
7. Give necessary output capacitance loads
8. Give necessary simulation commands

The opensource software called GUNA can be used for characterization. Steps 1-8 are fed into the GUNA software which generates timing, noise and power models.

### Timing Parameter Definitions

| slew_low_rise_thr | 20% value |
| --- | --- |
| slew_high_rise_thr | 80% value |
| slew_low_fall_thr | 20% value |
| slew_high_fall_thr | 80% value |
| in_rise_thr | 50% value |
| in_fall_thr | 50% value |
| out_rise_thr | 50% value |
| out_fall_thr | 50% value |

```
rise delay =  time(out_fall_thr) - time(in_rise_thr)

Fall transition time: time(slew_high_fall_thr) - time(slew_low_fall_thr)

Rise transition time: time(slew_high_rise_thr) - time(slew_low_rise_thr)

```



#Useful resources

- [cloud based verilof IDE/simulator](https://cloudv.io/)
- [Fault DFT toolchain](https://github.com/AUCOHL/Fault)
- https://github.com/efabless/chipignite-resources
- [SoCGen](https://github.com/habibagamal/SoC_Automation)