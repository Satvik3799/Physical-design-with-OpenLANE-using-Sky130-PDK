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

To implement an ASIC (Application specific Integrated circuit), certain steps need to be followed.

1. Architecture Design: Design the architecture and specifications of the chip. write a Verilog code for it.
2. RTL Design: Register transfer level (RTL) design involves creating a hardware description of the ASIC using HDL(Hardware description language) such as verilog, VHDL.
3. Functional Verification: The functionality of the design is verified repeatedly until it meets the desired specifications. This involves writing testbench, writing test cases and simulating multiple times. Testbench means an HDL code that puts specific inputs and checks the desired output, if the desired output is not met the design will be done again.
4. Synthesis (combination of above components to form a connected whole design) : The RTL design is transformed into a gate-level representation using a synthesis tool. The tool basically maps the RTL code to the target technology library, such as Sky130 PDK, and generates a gate-level netlist. A PDK is a physical design kit, which is a set of library given by a semiconductor foundry to enable a design and layout of IC. It allows Design engineers and foundry to be able to design a chip that is manufacturable. For ex. an NOT gate is a triangle with a bubble at the nose in a schematic, but physically the NOT gate is realized with CMOS transistors. So the schematic should be binded to the CMOS transistor equivalent of the same. The shape and I/O pins for a gate is defined in a library which is usually prebuilt and given in a PDK.
5. Floor planning: