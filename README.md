# Physical Design with OpenLANE using Sky130 PDK

Notion notes link - https://satvik3799.notion.site/Physical-Design-using-OpenLANE-with-Sky130-PDK-dfa6e85766e14a88ab9e8596e87e42c
Notion Lab detailed Guide link - https://satvik3799.notion.site/Results-only-e5cbc0e146e84fe3a0bf72ced4c87?pv=4

## Table of Contents
- [RTL to GDSII flow](#rtl-to-gdsii-flow)
	- [Stage-1 Synthesis](#stage-1-synthesis)
	- [Stage-2 Floorplan](#stage-2-floorplan)
	- [Stage-3 Placement](#stage-3-placement)
	- [Stage-4 Clock Tree Synthesis (CTS)](#stage-4-clock-tree-synthesis-cts)
	- [Stage-5 Power Distribution Network (PDN) generation](#stage-5-power-distribution-network-pdn-generation)
	- [Stage-6 Routing (TritonRoute)](#stage-6-routing-tritonroute)
	- [Stage-7 GSDII file](#stage-7-gsdii-file)
- [Steps to build custom standard cell and its integration](#steps-to-build-custom-standard-cell-and-its-integration)


# Physical Design using OpenLANE with Sky130 PDK

# Results only

# RTL to GDSII flow:

To see at which step the current def file is, use the command `echo ::env(CURRENT_DEF)` and it will give the current def file.

### Stage-1 Synthesis:
Move back up to [Table of contents](#table-of-contents)


1. For default Picorc32a design:
    
    $Flop \ ratio = \frac{no. \ of \ D-flip-flops \ used}{no. \ of \ cells \ used.}$
    
    here, flop ratio = 1613/14876 = 0.1084
    
    Chip area for module: $147712.918400 \ \mu m^2$
    
    ![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled.png)
    
    ![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%201.png)
    
2. For custom standard cell added Picorv32a design:
    
    The statistics report of the synthesis is can be found here - 
    
    Flop ratio = 1613/20121 = 0.08016
    
    Chip area module - $196832.528 \ \mu m^2$
    
    ![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%202.png)
    

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%203.png)

### Stage-2 Floorplan:
Move back up to [Table of contents](#table-of-contents)

For custom standard cell added Picorv32a design:
The new generated .def file after running floorplan can be found here: [floorplan.def](https://github.com/Satvik3799/Physical-design-with-OpenLANE-using-Sky130-PDK/tree/main/Design%20files/Floorplan)

Config.tcl file contents:

***Notice the utilization factor used.***

```c
# Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILE) "./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "12.000"
set ::env(CLOCK_PORT) "clk"

set ::env(CLOCK_NET) $::env(CLOCK_PORT)
set ::env(FP_CORE_UTIL) 40
set ::env(FP_IO_VMETAL) 4
set ::env(FP_IO_HMETAL) 3

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib" //notice double underscore before typical, fast and slow.
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
	source $filename
}
```

Floor plan result:

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%204.png)

Notice the equi-distant IO pads which are set manually via the tcl command:

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%205.png)

Zoomed in view of the cells:

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%206.png)

The Standard cell are placed at the bottom left temporarily.

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%207.png)

### Stage-3 Placement:
Move back up to [Table of contents](#table-of-contents)

For custom standard cell added Picorv32a design:

Placement .def file can be found here: [placement.def](https://github.com/Satvik3799/Physical-design-with-OpenLANE-using-Sky130-PDK/tree/main/Design%20files/Placement)

To view the file in magic tool, the general command is 

```magic -T <location and name of the .tech file of the PDK> lef read <location and name of the merged.lef file generated while preparing the design> read def <location and name of the def file>```

Placement result:

![Placement.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Placement.png)

The separately included standard cell - `sky130_vsdinv`

![VSDINV included.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/VSDINV_included.png)

Zoomed in cells.

![zoomed in cells.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/zoomed_in_cells.png)

### Stage-4 Clock Tree Synthesis (CTS)
Move back up to [Table of contents](#table-of-contents)

The new generated .def file after running CTS, with added clock buffers can be found here: [picorv32a.cts.def](https://github.com/Satvik3799/Physical-design-with-OpenLANE-using-Sky130-PDK/tree/main/Design%20files/CTS)

![timing.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/timing.png)

After running the Clock Tree Synthesis, the .def layout files gets updated along with synthesis.v file.

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%208.png)

The .def file after CTS:
 ![picorv32a.png](https://github.com/Satvik3799/Physical-design-with-OpenLANE-using-Sky130-PDK/blob/main/Design%20files/CTS/picorv32a.png)

### Stage-5 Power Distribution Network (PDN) generation:
Move back up to [Table of contents](#table-of-contents)

The Power and Ground nets get generated in this step. After running the command `gen_pdn` , the number of VPWR and VGND nodes generated can be seen.
The new generated .def file can be found here: [pdn.def](https://github.com/Satvik3799/Physical-design-with-OpenLANE-using-Sky130-PDK/tree/main/Design%20files/PDN%20Network)

![pdn_successful.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/pdn_successful.png)

Results and .def file view in magic tool.

![PDN_output.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/PDN_output.png)

### Stage-6 Routing (TritonRoute)
Move back up to [Table of contents](#table-of-contents)

Manually set Routing strategy is 0. `set ::env(ROUTING_STRATEGY) 0`.
The generated .def file and the extracted SPEF file can be found here: [picorv32a.def](https://github.com/Satvik3799/Physical-design-with-OpenLANE-using-Sky130-PDK/tree/main/Design%20files/Routing)

Routing takes time and uses memory. The current design was completed in ******1Hr14Min42Sec, ************and used **842 MB**.

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%209.png)

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2010.png)

Completed routing at 57th iterations, with zero violations in routing.

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2011.png)

The command has also ran SPEF extraction.

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2012.png)

Routing stats with layers:

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2013.png)

.def file output opened in Magic:

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2014.png)

### Stage-7 GSDII file
Move back up to [Table of contents](#table-of-contents)

The gds file can be found here - 

The .mag file can be found here - 

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2015.png)

Zoomed in view of the file.

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2016.png)

# Steps to build custom standard cell and its integration:
Move back up to [Table of contents](#table-of-contents)

Get the .mag file of a cell and extract its SPICE model.

Use magic to open the .mag file.

![CMOS inv.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/CMOS_inv.png)

Extract the SPICE file using these commands in tkcon console:

`extract all`

`ext2spice cthresh 0 rthresh 0` 

`ext2spice`

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2017.png)

Create a SPICE deck and Run a transient simulation of the SPICE file. The netlist and spice simulation parameters along with pmos and nmos paramters are as follows:

```c
* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

//.subckt sky130_inv A Y VPWR VGND
//X0 Y A VGND VGND sky130_fd_pr__nfet_01v8 ad=1.44n pd=0.152m as=1.37n ps=0.148m w=35 l=23
//X1 Y A VPWR VPWR sky130_fd_pr__pfet_01v8 ad=1.44n pd=0.152m as=1.52n ps=0.156m w=37 l=23

M1000 Y A VPWR VPWR pshort_model.0 ad=1.44n pd=0.152m as=1.37 ps=0.148 w=35 l=23
M1001 Y A VGND VGND nshort_model.0 ad=1.44n pd=0.152m as=1.52 ps=0.156 w=37 l=23
VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)

C0 VPWR Y 0.117fF
C1 A Y 0.0754fF
C2 A VPWR 0.0774fF
C3 Y VGND 0.279fF
C4 A VGND 0.45fF
C5 VPWR VGND 0.781fF

.tran 1n 20n
.control
run
.endc
.end
```

Running simulation with command `ngspice sky130_inv.spice` will generate a matrix of different values according to KCL and KVL equations. Running the command `plot y vs time a` in the ngspice console gives the following results:

![Untitled](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Untitled%2018.png)

Characterization of the custom inverter based on Rise transition, Fall transition, Cell Rise delay and Cell fall delay.

20% of 3.3V = 0.66V

50% of 3.3V = 1.65V

80% of 3.3V =2.64V

X-axis is the time axis and Y-axis is the Voltage. 

Red line - Output

Blue line - Input

**Rise Transition**: Time taken for the output to rise from 20% to 80% of max value.

![rise time.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/rise_time.png)

Rise transition = (Time to reach 2.6V) - (Time to reach 0.66V) = 2.23529 - 2.17647 = 0.058 ns

 

**Fall Transition**: Time taken for the output to fall from 80% to 20% of max value .

![Fall time.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Fall_time.png)

Fall transition = 4.0672 - 4.0396 = 0.0276 ns

**Cell Rise delay**: difference in time(50% output rise) to time(50% input fall). 

![Cell rise delay.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Cell_rise_delay.png)

Cell rise delay = 2.18228 - 2.1557 = 0.02658 ns

**Cell Fall delay**: difference in time(50% output fall) to time(50% input rise).

![Cell fall delay.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/Cell_fall_delay.png)

Cell fall delay = 4.05467 - 4.04467 = 0.01 ns

### Setting the grid parameters according to the tracks info.

The I/O, and power ports should be at intersection of horizontal and vertical tracks. The CMOS Inverter ports A and Y are on li1 layer. It needs to be ensured that they're on the intersection of horizontal and vertical tracks. We access the `tracks.info` file for the pitch and direction information:

![https://user-images.githubusercontent.com/86701156/124445507-3e508380-dd9d-11eb-8313-b45244931a86.PNG](https://user-images.githubusercontent.com/86701156/124445507-3e508380-dd9d-11eb-8313-b45244931a86.PNG)

Set the grid parameters according to the file in the tkcon console:

```
grid 0.46um 0.34um 0.23um 0.17um
```

A LEF file of the custom standard cell is extracted using the `lef write` command in the tkcon console, which creates a .lef file.

To integrate the custom standard cell in OpenLane config.tcl file must be edited with the following commands:

```c
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

When the OpenLane in invoked, after design preparation stage use the following commands to have the cell integrated into the design.
`set lefs [glob $::env(DESIGN_DIR)/src/*.lef]`
`add_lefs -src $lefs`
 and then run the synthesis with `run_synthesis` command. Which will include the new cell in the design. The results of cell integration are here:
 ![VSDINV included.png](Results%20only%20e5cbc0e146e84fe3a0bf72ced1b84c87/VSDINV_included.png)
