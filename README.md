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




## Day 2
### Chip Floor Planning Considerations
#### Theory

<details>
  
  <summary>Utilization Factor and Aspect Ratio</summary>
  ![p1](https://github.com/Srini-web/pes_pd/assets/77874288/2377e9ec-d58a-40d4-91ce-e338317fdd19)

- We consider a simple netlist with a Launch and Capture Flop. It also has an AND and OR gate.
- We then convert it into squares since we need appropriate dimensions

![p2](https://github.com/Srini-web/pes_pd/assets/77874288/cf97c0b4-9f68-4a91-b492-e11ccc9cee1f)

- Let us consider the areas of the gates and Flops as 1 sq unit
![p3](https://github.com/Srini-web/pes_pd/assets/77874288/b22c12ba-e187-4834-97ce-469f37c978be)


- Clubbing them together we get an area of 4 sq units

- The 'core' section of a chip is where the fundamental logic design is placed.
- The 'die' area contains the core and is a small semiconductor are on which the fundamental circuit is fabricated.

![p4](https://github.com/Srini-web/pes_pd/assets/77874288/4419eeb3-abdb-4d27-9d98-e903d1c48baa)

- Now we put the netlist in the 'core' area and check the utilization.
- Here
```
Utilization Factor = Area Occupied by the Netlist/Total Area of the Core
```
- As we can see here, there is 100% utilization and ```Utilization Factor = 1```.
- In practical scenarios we don't go for such a high utilization factor.
- The 'Aspect Ratio = Height/Width = 1'.
</details>


<details>
  <summary>Concept of Pre Placed Cells</summary>

  ![p5](https://github.com/Srini-web/pes_pd/assets/77874288/3600e3cc-4cc5-47c3-bf3c-154a1f50ce85)

- We take the above combinational logic as an example

![p6](https://github.com/Srini-web/pes_pd/assets/77874288/72d0fc09-02e1-4a41-acab-04262b44b210)

- We split the circuit into two parts, block 1 and block 2 as shown above

![p7](https://github.com/Srini-web/pes_pd/assets/77874288/d9b1d125-a349-4e51-892d-23509e3ab044)

- We extend the I/O pins and black box the boxes.
- Now we separate the boxes and the get their respective I/O ports.
- The use of doing this is that the users can use the blocks multiple times and form the required final circuit with ease.
- They only need to implement the design once and it can be reused.
- These kind of IPs have user defined locations and are placed in the chip before automated placement and routing takes place. These are called pre-placed cells.

**Surrounding Pre-Placed Cells with Decoupling Capacitors**

![p8](https://github.com/Srini-web/pes_pd/assets/77874288/9b61b290-5648-4f21-ac49-fc834ea77230)


- Huge capacitor filled with charge. The equivalent voltage across the capacitor is similar to what the power supply produces.
- We add the capacitor in parallel to the circuit.
- Everytime the circuit switches it draws current from the decoupling capacitor, whereas the outer network with the power supply and other componets is used to re-charge the capacitor
</details>

<details>
  <summary>Pin Placement</summary>
  
  - In pin placemnt step we use the HDL netlist to determine where a specific pin should be placed in the circuit.
  - We join the common pins and try to keep the connections as effecient as possible.
  - Pins are placed in the Die area.
</details>

<details>
  <summary>Cell Design and Characterization Flow</summary>

  **Cell Design Flow**
- Inputs -> Process design kits(PDKs) : DRC and LVS rules, SPICE models, library and user-defined specs.
- Design Steps -> Circuit Design, Layout Design(Euler Path and Stick Diagram), Characterization.
- Outputs -> CDL(Circuit Description Language), GDSII, LEF, extracted spice netlist(.cir)

**Characterization Flow**
- This is for an inverter.
1) Read the model files.
2) Read the extracted SPICE netlist.
3) Recognize the behaviour of the buffer.
4) Attaching the necessary power sources
5) Apply the stimulus, which is the input signal to the circuit.
6) Read the sub-circuit of the inverter.
7) Provide necessary output capacitances.
8) Provide the necessary simulation commands

**Timing Characterization**
- slew_low_rise_thr = 20%
- slew_high_rise_thr = 80%
- slew_low_fall_thr = 20%
- slew_high_fall_thr = 80%
- in_rise_thr = 50%
- in_fall_thr = 50%
- out_rise_thr = 50%
- out_fall_thr = 50%

- Propogation delay = time(out_fall_thr) - time(in_rise_thr)

- Transition Time
  - On rise: time(slew_high_rise_thr) - time(slew_low_rise_thr)
  - On fall : time(slew_high_fall_thr) - time(slew_low_fall_thr)
</details>

#### Labs

<details>
  <summary>Steps to run FLoorplan using OpenLANE</summary>

  ![s1](https://github.com/Srini-web/pes_pd/assets/77874288/316aa0c5-4b22-459e-acc0-31cc14191c9a)
    - To view floorplan we type
```
run_floorplan
```
in the OpenLANE shell.

![s2runfppic](https://github.com/Srini-web/pes_pd/assets/77874288/807a9743-f509-4185-a987-ebb3488f5316)


![s2runfppicPDNsucc](https://github.com/Srini-web/pes_pd/assets/77874288/f5a60ea8-8bf7-4570-aa73-cf12b46c1b67)

- To open the Floorplan we go to the required directory that is
```
vsduser@vsdsquadron:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/11-09_15-36/results/floorplan
```
using the ```cd``` command.

- Then we type the command:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

- The following layout is displayed

![s3](https://github.com/Srini-web/pes_pd/assets/77874288/d2e89620-dd13-4354-baaa-a91dbde13b2d)

- We can press 's' and then 'v' to align the design to the center of the screen.

- We can right click on the mouse and pess 'z' to zoom into a desired part.
![s4](https://github.com/Srini-web/pes_pd/assets/77874288/074ff4e0-2ded-4b0e-abba-23f2173adf79)

- We can see here that the I/O ports are equidistant

![s5](https://github.com/Srini-web/pes_pd/assets/77874288/5b830bcc-6271-4f37-9e5a-0f5bc6eb054d)

- We can check the details of the ports as follows
  - Hover over a port with your crosshair and press 's' on your keyboard
  - Now open the tkcon command window and type ```what```.
  - This will show you the details of the selected port.

![s6](https://github.com/Srini-web/pes_pd/assets/77874288/9021bb56-9847-4720-b914-bd602894dd78)

- If we zoom in a little more, we can see the tap cells.
- They are present to prevent latch up conditions which occur in the CMOS devices

![s7](https://github.com/Srini-web/pes_pd/assets/77874288/1d1ca9ba-7afe-4be4-b527-9b4ee9afb91c)

- These are the standard cells that are used in the design
</details>

<details>
  <summary>Library Binding and Placement</summary>

**Netlist Binding and Initial Place Design**


- In real life, the logic gates and cells do not have shapes, but are present in the form of rectangles and squares.
- Hence they have dimensions to them and the space where they are placed must be utilized carefully
- The above picture shows an example of a library.
- Library consists of various kinds of cells which have different shapes and sizes, flavours and different timing information.
- The components of the netlist are placed in the core area.
- They are placed according to the convenience of distance from the pins.
- When sending signal from FF1 to FF2, according to the circuit requirements, there has to be a very fast propogation of signals. Hence, they are placed very close and buffers are added since there is a small delay for the signal from the pin to reach FF1. The buffers maintain signal integrity

**Viewing the Placement**
- To view the placement we type
```
run_placement
```
in the OpenLANE shell.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/56832e08-c84e-4c78-8a24-73e1b9bdb05f)
- This is the result displayed. As we can see the '/picorv32a.placement.def' file is read.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/0adbe3c9-fe26-4770-9eb0-1ed9ca581402)
- We move one directory up from the 'floorplan' folder using
```
cd ../placement/
```
- To view the placement design we use the command
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```
![s8](https://github.com/Srini-web/pes_pd/assets/77874288/e600dc7a-f697-4925-ac3a-a82a7da5c540)

- The above is displayed.
- All these standard cells were present at the initial layout of the floorplan.

![s9](https://github.com/Srini-web/pes_pd/assets/77874288/299c7beb-b0c3-48f9-88f2-86f4cdba717b)

- If we zoom in we can see the placement of the standard cells in the standard cell rows.
</details>



