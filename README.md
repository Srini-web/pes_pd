# PES_PHYSICAL_DESIGN_WITH_OPENLANE
Name: Srinidhi B S        SRN: PES1UG20EC201 
## Day 1
### Inception of open-source EDA, OpenLANE and Sky130 PDK
#### Theory
<details>
  
  <summary>How to Talk to Computers</summary>

  - First we look at the introduction to the RISC-V ISA(Instructiion Set Architecture). Supposing we need to execute a C program on a particular hardware. First the C-program is converted into Assembly Code( here for RISC-V processor). Then the assembly code is converted into binary. An RTL implements this code for the particular layout of the RISC-V processor and the output is visible.
  - An application running on a system is usually written with the help of a high level language such as C,C++,Python etc. The code of these applications are compiled with the help of compilers running on a system software(OS). The compiler converts the high level code into assembly intructions for the particular processor. The assembler then converts the instructions into binary which is fed into the layout of the chip that processes every pattern of bits and the program is hence run.
</details>

<details>
  
  <summary>SoC Design and OpenLANE Computers</summary>

  **What is a PDK?**
- PDK stands for Process Design Kit.
- It is a collection of files used to model a fabrication process for the EDA tools used to design an IC
  - Process Design Rules.
  - Device Models
  - Digital Standard Cell Libraries
  - I/O Libraries
 
A simplified RTL to GDSII Flow is :
- Synthesis -> Floor/Power Planning -> Placement -> Clock Tree Synthesis -> Routing -> Signoff

- Synthesis - Converts RTL to a ciruit, out of compomments from the standard cell library.
- Floor and Power Planning - Obejctive here is to plan the silicon area and create robust power distribution network to power the chip.
  - Chip Floor Planning - Partition the chip die between different system building blocks and place the I/O pads.
  - Macro Floor Planning - We define the macro dimensions, pin locations and rows are defined.
  - Power Planning - The power distribution network is contructed.
- Placement - Placing the cells on the floorplan rows, aligned with the sites. There are 2 steps: Global and Detailed.
- Clock Tree Synthesis - To deliver the clock to all sequential elements.
- Routing - Implement the interconnect using the available metal layers.
- Sign Off - Perform physical verification such as DRC(Design Rule Check) and LVS(Layout vs Synthesis). Also perform STA(Static Timiing Analysis).
</details>

#### Labs  
<details>

  <summary>OpenLANE ASIC Flow</summary>

  ![p1](https://github.com/Srini-web/pes_pd/assets/77874288/48bb6b45-fab9-4d96-bf7b-3a64ff3532f4)

## Getting Familiar with the Open Source EDA Tools

### Design Preparation Step
<details>
  
  <summary>Openlane Interactive</summary>

- Let us first go the the working directory using the following commands
```
cd Desktop/work/tools/
```

```
cd openlane_working_dir/openlane/
```
![s1opln](https://github.com/Srini-web/pes_pd/assets/77874288/0e9392ba-b165-4482-8a6a-5c3a82c52946)

- We now type the command ```docker```.
- This will open the shell as shown in the figure above
- Now we type
```
./flow.tcl -interactive
```
![s2opl](https://github.com/Srini-web/pes_pd/assets/77874288/5e65d288-92de-4267-8ddd-0bc8853acda1)

- If the 'interactive' keyword is not present, then the entire flow of the tool is run.

![s4](https://github.com/Srini-web/pes_pd/assets/77874288/3c284bbc-e6e6-483c-b2a2-6ae41eea9062)

- Now we must import all the packages required to run the flow, we use the command:
```
package require openlane 0.9
```

![s3](https://github.com/Srini-web/pes_pd/assets/77874288/68250674-3ecd-4e74-ab0e-bd9b5e21a3d7)

</details>

<details>
  
  <summary>Running Synthesis</summary>
  - We will be working with the 'picorv32a' design.
  - The src folder has the verilog file and the sdc file of the design
  - Now we do the design setup stage using the command:
```
prep -design picorv32a
```
- After preparing the design, we can see that a new 'runs' folder is created.
  
![s5](https://github.com/Srini-web/pes_pd/assets/77874288/752fcce4-4d86-40cb-836f-e31a391dc5e2)

- To synthesize the design we type
```
run_synthesis
```
- This command invokes yosys, runs the synthesis and the abc commands.
- A long process is observed after typing this command, which for a little over two mintues.

![s7](https://github.com/Srini-web/pes_pd/assets/77874288/7816183c-5ec9-409d-9447-d02cb4419f23)

- A synthesis successful message must be displayed.

![s9](https://github.com/Srini-web/pes_pd/assets/77874288/b733a107-7da1-4527-8f22-b246f291cb80)

- The flop ratio can be calculated by using:
```
No. of flops/No. of cells = 1613/14876 = 0.108
```
- In percentage there is 10.8% of the total number of cells are Flops

![s8](https://github.com/Srini-web/pes_pd/assets/77874288/7308f813-8d85-4b59-9a24-d15d145e38c5)

- Under the runs folder we can check out the netlist file generated after synthesis
</details>
