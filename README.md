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

</details>

##  Day 2

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

## Day3
### Design library cell using Magic Layout and ngspice characterization
#### Theory

<details>
  
  <summary>Inception of Layout and CMOS Fabrication Process</summary>
     SPICE Deck Creation for CMOS Inverter
      - SPICE Deck is a netlist that has information on:
        - component connectivity 
        - component values
        - identifying the nodes
        - giving a designation to the nodes

**SPICE Simulation and Switching Threshold**

![p1](https://github.com/Srini-web/pes_pd/assets/77874288/c38f2267-31ae-41d4-9f29-010b4383c0ff)

- The CMOS on the right side has a bigger size than the one on the left.
- These waveforms tell us that the CMOS is a very robust device. The characteristics of the CMOS are maintained across a variety of sizes.
- The arrow is pointing to the point where 'Vin = Vout'.

![p2](https://github.com/Srini-web/pes_pd/assets/77874288/a9b6b073-4b4d-4dfa-a5cf-a1a42f33d4a5)

- Above graph gives details on each point and its significance

 </details>

<details>
  
  <summary>16 Mask CMOS Process</summary>

1) Selecting a Substrate - Selecting the appropriate substrate to synthsize the design on.
2) Creating active reagion for transistors - Adding layers of SiO2(40nm), Si3N4(80nm) and photoresist(1um). On top of the photoresist we put a mask layer. Pass UV light and remove the mask. Resist is removed. LOCOS(Local Oxidation of Silicon) is performed. Si3N4 is etched.
3) N-Well and P-Well formation - The next masks are used to create the source and drain regions of the MOSFETs. Boron is used to make P-Well using ion implantation. Phosphorus is used to create N-Well. Put the MOSFET in a Drive In furnace.
4) Formation of Gate - Gate formation involves depositing a gate oxide, defining gate patterns using photolithography, depositing gate material, etching to create gates, doping the substrate and insulating the gates.
5) Lightly Doped Drain Formation(LDD) - Lightly doped drain (LDD) formation involves implanting the drain and source regions of a MOSFET transistor with a lighter concentration of dopants to reduce hot electron effect and short channel effect and enhance device performance.
6) Source and Drain Formation - Source and drain formation in a MOSFET transistor typically involves doping the silicon substrate with chemicals such as arsenic or phosphorous for n-type regions (source and drain) and boron for p-type regions (source and drain). High temperature annealing is performed.
7) Steps to form Contacts and Interconnects(local) - Titanium is deposited with a process known as sputtering. Wafer is heated to about 650 - 700 C in an N2 ambient furnace for 60 seconds. TiSi2 contacts are formed.  TiN is also formed used for local communication. TiN is etched using RCA cleaning.
8) Higher Level Metal Formation - Forming contacts and interconnects locally involves depositing a dielectric material like silicon dioxide, patterning it using photolithography, etching contact holes, depositing a barrier metal (e.g., titanium or titanium nitride), filling with a conductor (e.g., aluminum or copper) using chemical vapor deposition (CVD), and then planarizing through chemical-mechanical polishing (CMP).
   
</details>

#### Labs
<details>
  
  <summary>A Git Clone and some other Steps</summary>

- We need to perform a git clone here from a repository that we require, to do the future labs.
- We can type the following command
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```

- Now we need to copy the 'sky130A.tech' file into the directory we just cloned
- We can do this by using
```
cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
```
![s2](https://github.com/Srini-web/pes_pd/assets/77874288/43fdcb64-188b-4467-8e76-d1de2cf45af3)

![s3](https://github.com/Srini-web/pes_pd/assets/77874288/c3702505-5c1f-4e13-987d-d2d70c33f8dd)

</details>

<details>
  
  <summary>Sky130 Basic Layers Layout and LEF using Inverter</summary>

- Now let us look at the layout of a CMOS inverter. To open this we type the command

![s4](https://github.com/Srini-web/pes_pd/assets/77874288/eaeeb1ac-6f46-4bd9-82ca-90350655c1a8)

```
 magic -T sky130A.tech sky130_inv.mag &
```

![s5](https://github.com/Srini-web/pes_pd/assets/77874288/be3296b1-f851-42fd-a4af-6bb21ec26965)

- We can get to know the details of the inverter by hovering the mouse cursor over it and pressing 's' on the keyboard. Then we can type ```what``` in the tkcon.
- Pressing 's' three times will show what parts are connected to the selected part.

- We shall look at the difference between LEF and Layout. The above image is a Layout.
- LEF represents abstract component data in a machine-readable format for IC libraries, while layout is the physical geometric arrangement of these components on a semiconductor chip.

</details>
  
<details>
  
  <summary>Steps to Create Standard Cell Layout and Extract Spice Netlist</summary>

- DRC errors can be viewed in the tkcon.

To extract Spice Netlist we perform the following steps in the tkcon window:
- We use the commands
```
ext2spice cthresh 0 rthresh 0 -> this is done to copy the parasitic capacitances
```
- The next command is
```
ext2spice
```

![s6](https://github.com/Srini-web/pes_pd/assets/77874288/4bdda6f9-cdee-4eaf-878f-1caeab914568)

- We can see that a sky130_inv.spice file is created
</details>

<details>
  
  <summary>Sky130 Tech File Labs</summary>
  
**Create Final SPICE Deck**
- To start off we look at the minimum value of the layout window.
- We can use 'g' on the keyboard to activate the grid and after selecting a grid by right clicking on the mouse, we type ```box``` in tkcon window to check the minimum value of the layout window.
- Next we need to open the spice file using the command
```
gedit sky130_inv.spice
```
- We need to configure it to the above specifications.

**Characterize Inverter using Sky130 Models**

![s7](https://github.com/Srini-web/pes_pd/assets/77874288/f6e64a7c-b00a-4dfc-8ee8-077c37dc7b6a)

- We now plot the graph for output vs input sweeping the time.
- We first use the command
```
ngspice sky130_inv.spice
```
- In the ngspice shell we use the command
```
plot y vs time a
```
- The following graph is displayed.
![s8](https://github.com/Srini-web/pes_pd/assets/77874288/73589a16-9ae9-4fa3-b2ed-19ce65981051)

- Rise Time -> time taken to rise from 20% to 80% of the max value -> 2.25075e-09 - 2.184e-09 = 0.006675e-09 s.
- Propogation Delay/Cell Rise Delay -> 2.21379e-09 - 2.15e-09 = 0.06379e-09 s.
</details>

<details>
  
  <summary>Sky130 PDKS and Steps to Download Magic Tool</summary>

- Enter the command
```
 wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
```
- Move the file to desktop using
```
mv drc_tests.tgz Desktop/
```
- Extract the file using
```
tar xfz drc_tests.tgz 
```
![s9magicdwn](https://github.com/Srini-web/pes_pd/assets/77874288/a656595e-dffa-4845-8cd7-59eb6c39e4e1)

- Do ```ls``` to view all the files in it.

To open the software we type
```
magic -d XR
```
- We click 'file' and open the 'met3.mag' file.
![s10magicaccess](https://github.com/Srini-web/pes_pd/assets/77874288/cd2733a1-35aa-4f54-9612-c6a124620d19)

- If we select an area and type ```drc why``` in the tkcon wndow, it will show us the DRC error.
- To add contact cuts to metal3, first select an area using left and right click. Then hovering over the m3contact we click middle mouse button.
  
</details>

<details>
  
  <summary>Fixing DRC Errors</summary>

- There is a DRC error in the poly.mag file in 'poly.9'.
- Open the sky130A.tech file in the editor and make the following changes
  
![s11polyedit](https://github.com/Srini-web/pes_pd/assets/77874288/60e2ac10-0da4-44d9-b580-d4f10f89766d)

- Now open the tkcon window and type
```
load tech sky130A.tech
drc check
```
![s12polyerror](https://github.com/Srini-web/pes_pd/assets/77874288/23a9d796-430c-487a-8556-2891ecd1a8b7)

- As we can see the error is fixed.

**DRC Error as Geometrical Construct**
- We open the nwell.mag file.
```
cif ostyle grc
cif see dnwell_shrink
cif see nwell_missing
```
- We type the above commands
- The following is displayed

  ![s14nwellerror](https://github.com/Srini-web/pes_pd/assets/77874288/897c7e2d-2120-4201-aca4-0ac6c6405602)
</details>

## Day 4
### Pre-layout timing analysis and importance of good clock tree
#### Labs
<details>
  
  <summary>Convert Grid Info to Track Info</summary>
- We must go to the following directory and type
```
less tracks.info
```

![s1lesstracksinfo](https://github.com/Srini-web/pes_pd/assets/77874288/69dddfc6-10d4-4dc2-85d9-cbae0c0d3342)

- The 'tracks.info' file is used during the routing stage.
- Routes are the metal traces.
- Since the PNR is an automated flow, we need to specify where all we want the routes to go.
  ![s2grid](https://github.com/Srini-web/pes_pd/assets/77874288/6b1142cb-0354-4fdf-bb83-08f249c0a381)

- Now we converge the grid definition in the layout to track definition, by typing the following command
![s2 2grid](https://github.com/Srini-web/pes_pd/assets/77874288/62816399-9f33-4a35-bc7d-eb3d00a283b3)

- The following is the result.
- This shows that the routing of 'li1' layer can happen only along this grid

![s3ay](https://github.com/Srini-web/pes_pd/assets/77874288/3d680298-893a-47fc-a02c-7cc54c8d7514)

- Having the ports at the intersection of horizontal and vertical tracks ensure that the route can reach that port from the 'y' as well as 'x' direction.
- The next requirement is that the width of the cell should be the odd multiple of xpitch which is '0.46' as seen in the 'tracks.info' file.
- As we can see it encloses two full boxes and two halves of one box, totally making three boxes as indicated by the white rectangle.
- 
</details>

<details>
  
  <summary>Convert Magic Layout to Standard Cell LEF</summary>
**Convert Magic Layout to Standard Cell LEF**
- In the tkcon window of the 'sky130_inv.mag' file we type
```
save sky130_vsdinv.mag
```
to make our own .mag file
- To make the .lef file we type
```
lef write
```
to make our own lef file.

- Type ```less sky130_vsdinv.lef```.

![s4lefwrite](https://github.com/Srini-web/pes_pd/assets/77874288/e5cb32e3-2253-4f0f-8b98-c6ba06c47e45)

- The .lef file is as follows
  
![s5openlef](https://github.com/Srini-web/pes_pd/assets/77874288/c65df412-5cee-49a1-8592-5a7ca43a1587)

</details>
<details>
  
  <summary>Steps to Include New Cell in Synthesis</summary>

- We copy the .mag file that we created to the 'src' folder of picorv32a folder.
  ![s6filecopy](https://github.com/Srini-web/pes_pd/assets/77874288/0af8e96f-5d52-4918-8842-8ea7986030e3)

- We then perform this copy command.
![s7configedit](https://github.com/Srini-web/pes_pd/assets/77874288/c0c01abb-b191-48fc-8872-3d2141627831)

- Next we modify the 'config.tcl' file in the picorv32a folder as follows.
- Open the OpenLANE interactive window and retrieve the 0.9 package.
![s8openlane](https://github.com/Srini-web/pes_pd/assets/77874288/13e36e31-ae75-40bc-ab36-e425bf231899)

- Type the following
```
prep -design picorv32a -tag 16-09_19-58 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs 
```
- Next we type ```run_synthesis```.

![s9synth1](https://github.com/Srini-web/pes_pd/assets/77874288/59c24d91-767b-45c9-a42e-d3e64b2d6cc0)

- The following results are displayed.
![s10synth2](https://github.com/Srini-web/pes_pd/assets/77874288/d7481d02-2e85-483d-ba2d-407c6b37efd0)

- To run floorplan and placement we type
```
init_floorplan
run_placement
```
![s10synth2](https://github.com/Srini-web/pes_pd/assets/77874288/89b4c53d-0e7c-476f-bc5d-71e7a383163d)

- Now to view the design we type the command
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![s12placement](https://github.com/Srini-web/pes_pd/assets/77874288/ea3005d0-e8d3-4968-8598-b9eeb5706d72)

- The following is displayed.
- Zooming into the design using 'z' we can see the sky130_vsdinv than we defined.
- We have plugged in our custon cell in the OpenLANE flow.
</details>

###### The Floorplan zoomed in and instace id as filled in the sheets
<details>
  <summary>The Floorplan zoomed </summary>
  
   ![INSTANCE](https://github.com/Srini-web/pes_pd/assets/77874288/14360d13-8fde-4878-bce1-d4ddd873ffa1)
   
</details>

<details>
  
  <summary>Timing Analysis with Ideal Clocks using OpenSTA</summary>

**Configure OpenSTA for Post-Synth Timing Analysis**
- We must create two files

![p13](https://github.com/Srini-web/pes_pd/assets/77874288/982ac92f-1a3f-43d8-a960-9a3728806806)

- The first one must be in the openlane directory
- This file is known as the 'pre_sta.conf' file.

![p14](https://github.com/Srini-web/pes_pd/assets/77874288/b48945a1-4ec6-4972-be3d-913a34b88f9a)

- The second is the my_base.sdc file.
- This should be in the 'src/sky130' directory under the picorv32a directory.

- To run the timing analysis we type
```
sta pre_sta.conf
```
<img width="551" alt="p15" src="https://github.com/Srini-web/pes_pd/assets/77874288/70547d14-0f73-4f4c-982f-81a8d6ce426c">

- Following result is displayed
- There is a slack violation

![p16](https://github.com/Srini-web/pes_pd/assets/77874288/94ce30cc-2e58-4955-a127-58883826896b)

- Settinf MAX_FANOUT value to 4 reduces the slack violation.

</details>

###### Clock Tree Synthesis TritonCTS and Signal Integrity
<details>
  
  <summary>Run CTS</summary>

- To run CTS we need to type the command
```
run_cts
```

![s17ctsrun](https://github.com/Srini-web/pes_pd/assets/77874288/76efea91-25af-4bcc-834d-2a3546f6dd3b)

- New .v is created
  
![s18vfiletrim](https://github.com/Srini-web/pes_pd/assets/77874288/7fb38b09-7381-45b6-8b0d-8d79b8c0e831)

</details>

<details>
  
  <summary>Timing Analysis with Real CLocks using OpenSTA</summary>

- First we type the command
```
openroad
```
- Then we read the .lef file using the command
```
read_lef /openLANE_flow/designs/picorv32a/runs/16-09_19-58/tmp/merged.lef
```
- Then we read the .def file.
```
read_def /openLANE_flow/designs/picorv32a/runs/16-09_19-58/results/cts/picorv32a.cts.def
```

![s19opnrdlefdef](https://github.com/Srini-web/pes_pd/assets/77874288/d90a65d1-102e-4bcd-95ab-5536549f22e7)

- We then do
```
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/16-09_19-58/results/synthesis/picorv32a.synthesis_cts.v
read_liberty -max $::env(LIB_SLOWEST)
read_liberty -max $::env(LIB_FASTEST)
```
- We read the .src file.
```
read_sdc /openLANE_flow/designs/picorv32a/src/sky130/my_base.sdc
```
- We set the clock
```
set_propagated_clock [all_clocks]
```
- Checking the report
```
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```

![p20sdcop1](https://github.com/Srini-web/pes_pd/assets/77874288/7ee00b5d-4b61-413a-9427-d936f44efe07)

- Above results are displayed

![p21abvrslt](https://github.com/Srini-web/pes_pd/assets/77874288/c145e05b-75cb-46b5-b8ff-cd87c84deae4)

- We perform it again for a more accurate result

![s22accurate](https://github.com/Srini-web/pes_pd/assets/77874288/9b0dfe39-aafd-48a0-81a8-5d7ed7877d67)

- Above results are displayed

![p22last](https://github.com/Srini-web/pes_pd/assets/77874288/c5df6ad9-cb00-40c0-b418-59c759ae71ac)

```
report_clock_skew -hold
report clock_skew -setup
```

