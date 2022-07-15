# OpenLane PD Workshop
# Introduction
OpenLANE is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, Fault, OpenPhySyn, SPEF-Extractor and custom methodology scripts for design exploration and optimization. It is a tool started for true open source tape-out experience and comes with APACHE version 2.0 . The goal of OpenLANE is to produce clean GDSII without any human intervention. OpenLANE is tuned for Skywater 130nm open-source PDK and can be used to produce hard macros and chips.
![image](https://user-images.githubusercontent.com/107121814/179245608-99321bec-4e24-4068-8c45-81c8e1bb981b.png)

# OpenLane detailed ASCI workflow
![image](https://user-images.githubusercontent.com/107121814/179245220-3d4b0061-cda8-4e8f-838e-f653b8326c48.png)
# Stages in Openlane flow and tools used
OpenLane flow consists of several stages.

## 1. Synthesis

Yosys: Used for RTL synthesis

abc: Performs cell mapping as per PDK.

OpenSTA: Used for static timing analysis on the resulting netlist for timing closure.

## 2. Floorplan and PDN

OpenROAD: is used for this step. Placement is done in two steps, one with global placement in which tool roughly places the design across the chip. In detailed placement, proper placemennts happens which fit in the standard cell rows.

## 3. CTS

TritonCTS - Synthesizes the clock distribution network

## 4. Routing

FastRoute - Performs global routing to generate a guide file for the detailed router

TritonRoute - Performs detailed routing from global routing guides

SPEF-Extractor - Performs SPEF extraction that include parasitic information

## 5. GDSII Generation

Magic - Streams out the final GDSII layout file from the routed def

# Direcotry Structure
![1](https://user-images.githubusercontent.com/107121814/179249245-2edf86f4-2a6f-4c61-a74c-3cf043d74cc6.PNG)

## inside the design dir picorv32a
![2](https://user-images.githubusercontent.com/107121814/179249399-022f9d53-f7b7-4e8f-b51c-ceee3bf38b82.PNG)
# Running the Labs
# 1. Design Preparation Step
* run "docker" command to create required environment.
* execute "./flow.tcl -interactive" to run the OpenLANE flow in interactive mode.
* import openlane package using "package require openlane"

![3-running openlane](https://user-images.githubusercontent.com/107121814/179249873-a8459ebb-d5ab-4c81-9b34-c544176b42ce.PNG)

* prep -design picorv32a
![4-design_prep](https://user-images.githubusercontent.com/107121814/179249924-58e06a90-d98e-4fc2-b309-b5c43a8c1c39.PNG)

Output after design prep

![5-run_dir](https://user-images.githubusercontent.com/107121814/179250751-4213ec62-0618-47cf-8fe2-b52dd8b6b0c0.PNG)
config.tcl after design prep
![6 1 config tcl after design prep](https://user-images.githubusercontent.com/107121814/179250965-541643cc-3d6c-4aaf-a6d2-2b87392da7e9.PNG)

# 2. Synthesis

Synthesis is the process of transforming the RTL into a gate-level netlist, honoring all the specified constraints and optimization settings.

Snapshot after synthesis is completed

**Command used: run_synthesis**
![7 1- synthesis done](https://user-images.githubusercontent.com/107121814/179251990-77a0cf9d-89aa-4d13-aba2-fc585bb75ad9.PNG)
Reports after synthesis

![9  reports-synthesis](https://user-images.githubusercontent.com/107121814/179252117-94ebb402-e838-497a-a272-474a1bdbd906.PNG)

cell statistics

![11- synthesis reports - 1-yosys_4 stat rpt](https://user-images.githubusercontent.com/107121814/179270229-54645c0e-751e-4dc8-8a86-460f453057f6.PNG)

Initial Timing Analysis Report

![12- synthesis reports - 2-openSTA_rpt](https://user-images.githubusercontent.com/107121814/179270280-db6d3f49-9461-4459-affb-8311b0f9b3a3.PNG)

Results after syntehsis
![9  reports-synthesis](https://user-images.githubusercontent.com/107121814/179270179-d4d6617e-093f-47a8-8a14-33ec15fee675.PNG)

## Flop ratio from synthesis report [1-yosys_4.stats.rpt]
Number of DFF : 1613; 
Number of cells : 14876

Flop Ratio : 1613/14876 = 10.8%

# 3. Floorplanning
In Floorplanning we typically set the:

* Die Area
* Core Area
* Core Utilization
* Aspect Ratio
* Macros Placement  (Standard cell placement is not done in floorplan but in placement)
* Power distribution network (typically done during floorplan but in OpenLANE this is done later)
* Place input and output pins

Variables used in floorplan can be found in **Openlane/configurations/README.md**

![image](https://user-images.githubusercontent.com/107121814/179273958-4496b805-6c08-4da4-a67a-3d7d78d03156.png)

Lowest priority – system defaults;  Next config.tcl in design dir;  Next pdk variant of the tcl file

**Command used to do floorplanning in Openlane : run_floorplan**
![14- run_floorplan](https://user-images.githubusercontent.com/107121814/179274422-e60d9a1f-41ae-4210-9633-1ac3a8507f7c.PNG)

The output the the floorplanning phase is a DEF file

DEF after floorplan
![16 def after floorplan](https://user-images.githubusercontent.com/107121814/179274483-6a7f81f3-0b1a-4a8e-bb71-d278344fd952.PNG)

Viewing floorplan def

magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef  def read picorv32a.floorplan.def &

![image](https://user-images.githubusercontent.com/107121814/179274732-e04becce-19fe-46c4-b180-30bab4abb963.png)

![image](https://user-images.githubusercontent.com/107121814/179274856-5ec10c3e-4c0c-4b93-8a3e-8e1b69ec9f86.png)

**few shortcuts for using magic tool**
* v- for Zoom Fit
* S- for selection
* Z-for zooming in
* ^Z-for Zooming out
* Once an item is selected,  go to tkcon window and type what to get more info on the` selected item

Zoomed in views

![image](https://user-images.githubusercontent.com/107121814/179274979-8c510cd4-ea40-4438-914e-fe854c143c3d.png)

![image](https://user-images.githubusercontent.com/107121814/179275023-17162d3d-8783-497c-81bd-c2d3a185aedf.png)


# 4. Placement
Placement of standard cells takes place in this stage. Congestion aware placement is done using RePIAce.  OpenLANE does placement in two stages:

1. Global Placement - Optimized but not legal placement. Optimization works to reduce wirelength by reducing HPWL(half parameter wirelength). Standard cells are placed in standard cell rows but there may be some overlaps.
2. Detailed Placement - Legalizes placement of cells into standard cell rows while adhering to global placement (no overlaps)

Optimizing placement
We estimate wire length and capacitance and based based on these insert repeaters  to solve signal integrity problems. THis is at the cost of area.

**Command to run placement : run_placement**

![image](https://user-images.githubusercontent.com/107121814/179278192-7ed95d5e-ded9-4570-870d-c7961d7eb838.png)

![image](https://user-images.githubusercontent.com/107121814/179278332-fec38359-edee-457e-b1c3-6d4953788bbe.png)

![image](https://user-images.githubusercontent.com/107121814/179278410-e4045f09-34d1-4315-8c89-857e5a13714a.png)


Placement result files:

![19  placement output files](https://user-images.githubusercontent.com/107121814/179279248-f586b3c3-90e6-44f5-9802-0a1b158cfc99.PNG)


Viewing placement def

_magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef  def read picorv32a.placement.def &_

![20  magic after placement](https://user-images.githubusercontent.com/107121814/179279350-aed7eb91-b437-4e54-a87e-8407ea83ab42.PNG)


Zoomed-in view

![21  magic after placement-zoomed in](https://user-images.githubusercontent.com/107121814/179279507-709c627f-1ad3-4330-88ce-5ffa20be5346.PNG)



![image](https://user-images.githubusercontent.com/107121814/179278612-891a5ab1-91c7-404a-a215-6e70f79f337c.png)


**NOTE:* PDN usually happens in floorplan step. But in openlane flow the order is different. Post floorplan and post cts, we do PDN


# Cell Design and Characterization Flows

1. Inputs - PDKs, DRC & LVS rules, SPICE models, library & user-defined specs.
2. Design Steps - Software GUNA used for characterization.
  ![image](https://user-images.githubusercontent.com/107121814/179282800-e5394ebf-bbab-4dbc-81b3-6cf6fc9e045a.png)

3. Outputs - Outputs are CDL, GDSII, LEF, extracted Spice netlist (.cir), & provide Timing (delays, slews), Power, Noise, function informataion in .lib format

  ==> .lib information is important for PD analysis.


# Design Library Cell Using Magic Layout and ngspice Characterization

We are not  designing a standard cell from scratch. We will clone an already existing git repo

git clone **https://github.com/nickson-jose/vsdstdcelldesign.git**

![22  git clone](https://user-images.githubusercontent.com/107121814/179284923-23ad1009-b4f4-4c3f-8454-402676d10d47.PNG)

![image](https://user-images.githubusercontent.com/107121814/179285005-d61771bc-fd21-4d28-9d2d-7add57fdcaa9.png)

**Viewing the inverter layout**

cd /home/aleena.emmanuel/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

cp ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A-GDS.tech .

magic -T sky130A-GDS.tech sky130_inv.mag &

![image](https://user-images.githubusercontent.com/107121814/179285226-0433312a-3437-494f-8865-4bee5c8b02bb.png)

pmos connectivity

![30  inverter-pmos-connectivity](https://user-images.githubusercontent.com/107121814/179285397-5a126997-12f1-4897-8088-ec371b45524a.PNG)


![image](https://user-images.githubusercontent.com/107121814/179287275-2ce44521-495e-4918-b630-2788fb0b8c2b.png)


**DRC Errors**

DRC errors in magic will be highlighted with white dotted lines: Steps to create DRC error:

We can remove some metal area to create DRC errors

Select area & press "d"

![image](https://user-images.githubusercontent.com/107121814/179285936-3445936f-0bae-4220-b1ca-6f34caff6fa9.png)

## **Extracting SPICE Netlist**

To find the logical functioning of the inverter, we will first extract the spice netlist from the layout and post that we will do simulations o using ngspice

Follow the steps in the below images in the tkcon window
* extract all

![image](https://user-images.githubusercontent.com/107121814/179287290-a6219c3a-02a8-4fbe-95ee-6a6ba355c569.png)

less sky130_inv.ext

  ![image](https://user-images.githubusercontent.com/107121814/179287359-5ed9cfbe-eeee-4c92-8950-d41ed57fd24f.png)

  Use this ext file s to create a spice file to be used with ngspice tool

* ext2spice cthresh 0 rthresh 0 – no new file created

* ext2spice – creates spice file sky130_env.spice

  less sky130_inv.spice

  ![image](https://user-images.githubusercontent.com/107121814/179287840-783b27a2-1556-4b1f-bbe5-336189b73e0d.png)


## Creating the final SPICE deck using sky130 tech

Edit the sky130_env.spice.   In spice, X subscript is used when we are invoking existing subckt (i.e., if we have .subckt nshort_model.0 ). Since here we are invoking nmos and pmos models directly, we need to change X0 to M0 and X1 to M1.

![image](https://user-images.githubusercontent.com/107121814/179288096-9e3674f0-6d00-4a95-b8ba-71ede6eedcf7.png)


## Running Transient simulation of CMOS inverter

ngspice sky130_inv.spice

![image](https://user-images.githubusercontent.com/107121814/179288563-d54e0eb8-66cb-4b92-996c-4d8e71827c7e.png)

## Plotting transient response

plot y vs time a

![image](https://user-images.githubusercontent.com/107121814/179288793-12f0997f-f5c5-41d6-bccd-c1692b077a34.png)


# Characterizing the cell : Finding Rise Transition, Fall Transition, Rise Cell Delay, Fall Cell Delay

Zoom into the transient analysis output waveforms and find out the above values

1. Rise tansistion - 2.23845e-09 - 2.17936e-09=  0.05909
2. fall transition - 4.04992e-09- 4.09271e-09 = -0.04279
3. Rise delay - 1.65005 - 1.65 = 0.05654 
4. fall delay - 4.07473e-09- 4.05e-09 = 0.02473

# Generate Layout info from Magic

Magic can extract layout info in LEF format for PNR Tools. Following rules need to be taken care: 
1. I/P & O/P ports must lie on the intersection of vertical & horizontal tracks 
2. Width of std cell should odd multiple of track pitch
3. Height should be odd multiple of track vertical pitch

less /home/aleena.emmanuel/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/tracks.info

![image](https://user-images.githubusercontent.com/107121814/179290250-158fad9f-3e20-4621-92e5-d7733245444a.png)
li1 – X (horizontal track) – offset of 0.23 and pitch of 0.46;  Y (vertical tracks) – offset 0.17 and pitch 0.34

Converge the grid with track values to check if our ports A and Y are actually on the intersection of x and y of li1
![image](https://user-images.githubusercontent.com/107121814/179290794-2f438c88-34af-41a3-a81a-ca0a44d649df.png)

![image](https://user-images.githubusercontent.com/107121814/179291359-e6213476-137f-41b9-a387-6c9659e40c0a.png)

# Generating LEF

Command : write_lef (in tkcon window)

![image](https://user-images.githubusercontent.com/107121814/179291817-ebf3a0fb-a199-4f08-8b0c-db8f9ece2aa7.png)

less sky130_vsdinv.lef

![image](https://user-images.githubusercontent.com/107121814/179291879-7dcf14c5-995b-45f6-90d2-d10a08e5bc38.png)

# Plugging in the custom Inverter into Openlane flow

1. Copy the LEF of inverter cell along with the lib files containg the characterized data for the custom cell in <design>/src folder.
    cp ../../../vsdstdcelldesign/libs/sky130_fd_sc_hd__*
    cp ../../../vsdstdcelldesign/sky130_vsdinv.lef .

  

2. Open <design>/config.tcl and set env variables for syntesis and timing libraries lib files from src folder. Overwrite previous run to include new configuration switches:
    ![image](https://user-images.githubusercontent.com/107121814/179293473-23ad3bd2-6b1d-46f9-b010-9f02c4b59788.png)
    ![image](https://user-images.githubusercontent.com/107121814/179293514-b8b95278-67f7-4296-a7c2-572bc1fe1a55.png)

3.  include extra cell LEF
  
    _set lefs [glob $::env(DESIGN_DIR)/src/*.lef]_
  
    _add_lefs -src $lefs_
  
    ![image](https://user-images.githubusercontent.com/107121814/179294543-84f18bfc-c5d4-4572-b7ee-af593c0e6fbc.png)
  
4. run_synthesis
    ![image](https://user-images.githubusercontent.com/107121814/179294709-e0ea2fc2-cc93-4c00-8979-cd4bca61b9fb.png)

    Our custom cell is plugged in :** 1554 instances of the cell**

#  synthesis settings to fix slack 
For the design to be complete, the worst negative slack needs to be above or equal to 0.  
  If the slack is outside of this range we can do one of multiple things:

* Review our synthesis strategy in OpenLANE
* Enable cell buffering
* Perform manual cell replacement on our WNS path with the OpenSTA tool
* Optimize the fanout value with OpenLANE tool

Following iterative steps are done for same:

* report_check
* Change Fanout - for higher slews SYNTH_MAX_FANOUT
* report_check
* Replace cells (if cap is high ). This will take hit on area.
* report_check
  
Example:
  
Before delay-area optimization following are the area and dealy stats for the design.
  ![image](https://user-images.githubusercontent.com/107121814/179295772-9defb4fa-caed-4640-80bf-53a3cedc74de.png)

Lets try to make the synthesis timing driven, we need to strike a  balance between the delay and area

![image](https://user-images.githubusercontent.com/107121814/179296158-03591626-48c9-4b3c-abb6-c2a95c230643.png)

After redoing synthesis

![image](https://user-images.githubusercontent.com/107121814/179296209-0cd595ae-4b0a-41e0-bbef-fe234dba6229.png)
![image](https://user-images.githubusercontent.com/107121814/179296220-26c70e0a-e9fa-4c20-be60-11829db43b2f.png)  
  
# Floorplan/placement after redoing synthesis
 
run_floorplan is giving errors. So follow the below steps

   init_floorplan
   ![image](https://user-images.githubusercontent.com/107121814/179296990-1d9be54c-4130-4239-b2b8-a3fb7b3977ab.png)
  
   place_io
    ![image](https://user-images.githubusercontent.com/107121814/179297058-c000eba3-a87a-47bc-8150-f4c854b32d06.png)

  
   global_placement_or
    ![image](https://user-images.githubusercontent.com/107121814/179297088-457bafbb-de04-419c-9272-0b396a718ab9.png)

  detailed_placement
    ![image](https://user-images.githubusercontent.com/107121814/179297141-95faa627-94e4-488c-bd14-5e1e22f0fc6f.png)

    ![image](https://user-images.githubusercontent.com/107121814/179297170-f5eb0066-da05-4cc3-b17b-846ede9d15ab.png)

    
   tap_decap_or
    ![image](https://user-images.githubusercontent.com/107121814/179297189-8d3d6686-b8bf-4804-b573-8ee7e4660a1d.png)

  run_placement
    ![image](https://user-images.githubusercontent.com/107121814/179297235-756acb31-b42f-4f1d-8308-b0ec794a1723.png)

  **Viewing the placement def**
  _magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef  def read picorv32a.placement.def_
  
  ![image](https://user-images.githubusercontent.com/107121814/179297667-40ce9ed9-664c-4d12-b391-cdba6c278c93.png)

  ![image](https://user-images.githubusercontent.com/107121814/179297689-ebe354ad-cbc1-48cd-b2a3-caea85b746f6.png)
  
  Overlap is called abetment; this is to ensure that power/grd rails are shared btw cells
  
  We have successfully plugged in our custom cell into openlane flow.

![image](https://user-images.githubusercontent.com/107121814/179297828-6ff9e361-872b-4e53-a14c-8c4ea308653e.png)
  
![image](https://user-images.githubusercontent.com/107121814/179297857-eb4b1485-e0e0-43fe-a88f-63b1ec7782a4.png)

# configuring OpenSTA for post-synthesis timing analysis
  
Create a new sta config file pre_sta.conf

vi openlane/pre_sta.conf

![image](https://user-images.githubusercontent.com/107121814/179298023-f574b057-01bc-4a30-800d-4fba7ef5282b.png)

  
vi my_base.sdc (SDC file for sta analysis)

  ![image](https://user-images.githubusercontent.com/107121814/179298074-6c58e51b-66fd-4b43-a26e-bb7ffdc468af.png)

    
Command : sta pre_sta.conf

![image](https://user-images.githubusercontent.com/107121814/179298882-be074021-fcac-410d-b5d3-9d2c26ad4d4d.png)

set SYNTH_MAX_FANOUT to 4 and rerun synthesis (Reducing fanout reduces delay)
  
![image](https://user-images.githubusercontent.com/107121814/179299363-a468aa7e-5639-4e4d-b25e-373092429d5d.png)

Post the slack modifications we have to ensure that the openlane flow has the new netlist
  
  Command: write_verilog
  
    ![image](https://user-images.githubusercontent.com/107121814/179299800-9c0b91d1-51e7-421d-9456-0b8c18f1e5f2.png)

# 5. Clock Tree Synthesis (CTS)

TritonCTS is used for CTS.
  
Clock buffers get added in CTS  
  
**Command : _run_cts_**
  
  ![image](https://user-images.githubusercontent.com/107121814/179299928-636731b6-0373-4fd8-999f-26bcd1924ad1.png)

  ![image](https://user-images.githubusercontent.com/107121814/179299941-ead82a35-b698-4e7e-829b-55ac23d425af.png)
  
  Post synthesis, a new verilog netlist gets created. The *cts.v contains the previous picorv + clock buffers
  ![image](https://user-images.githubusercontent.com/107121814/179300051-07ebec51-6691-42f7-9ea9-1a443bd7ae59.png)
  
  Outputs of CTS
    ![image](https://user-images.githubusercontent.com/107121814/179300094-f4cd53bd-887a-4b7b-bde8-6f522a665800.png)

Verifying CTS run
  ![image](https://user-images.githubusercontent.com/107121814/179300472-23669ac1-6a5d-48e1-b2c3-cdcc2f8e2436.png)

# Analyze timing with real clocks using OpenSTA
  
Enter openroad and create a db form def and lef
  
  ![image](https://user-images.githubusercontent.com/107121814/179300723-9007a3c9-26da-4f3b-8446-372d5a105ccb.png)

  ![image](https://user-images.githubusercontent.com/107121814/179300769-08542fcb-cf6c-4494-bcc1-47cbf0899554.png)

  _report_checks -path_delay min_max -format full_clock_expanded -digits 4_ : to report min max paths worst slacks.
  
  ![image](https://user-images.githubusercontent.com/107121814/179300809-7b088cb2-cc45-46cd-9ba1-445ea7f13144.png)

  ![image](https://user-images.githubusercontent.com/107121814/179301095-3b816aa5-1ea6-475d-89fe-c25c7fd9771f.png)

  We built the clock tree for typical corner, but did anlysis for min and max corners. So the analysis s incorrect.

Execute OpenSTA with right timing libraries and CTS assignment  
  
  ![image](https://user-images.githubusercontent.com/107121814/179301227-6b02238b-b013-433f-b272-ecfe680e67f8.png)

  ![image](https://user-images.githubusercontent.com/107121814/179301246-2ebc9afc-91a9-45d3-b9c5-a2b2145e42a7.png)

 ## Experiment : removing cells in clock buffer cell list. 
  
  ![image](https://user-images.githubusercontent.com/107121814/179301447-6bda7e5f-2259-45f0-bfac-ecd109e28b86.png)

  ![image](https://user-images.githubusercontent.com/107121814/179301535-84056e29-e96f-4180-99f6-05dbc6d2a8c3.png)

  ![image](https://user-images.githubusercontent.com/107121814/179301550-9dd5adcf-7e72-4be6-a97e-846e95c4c92c.png)

  Note : Reinsert the removed clock buffer with drive 1 back into CTS_CLK_BUFFER_LIST variable.
  
# 6. Generating PDN and Routing
  
## PDN generation  
  
Visual representation of power delivery network
  ![image](https://user-images.githubusercontent.com/107121814/179302588-f5b5a9b6-195e-4148-8e3d-6528888e9838.png)
  
  Power flows from:
  1. Outside to Pads
  2. Pads to ring
  3. Ring to power straps
  4. Straps to STD cells
  
  Command : gen_pdn
  
  ![image](https://user-images.githubusercontent.com/107121814/179302369-1cf0fc11-915d-4753-a28f-b41d2f9e23e5.png)

  Standard cell rail info considered by the tool
  ![image](https://user-images.githubusercontent.com/107121814/179302384-b5437602-c77b-43df-85f8-f8079f7feb00.png)

  ![image](https://user-images.githubusercontent.com/107121814/179302398-4947c623-7e25-48d4-b9db-80eab20117a3.png)
  
## Routing

  Maze Routing:

  1. Connect source to Target
  2. Priority: L shape, Z shape, zigzag
  3. Rule of thumb: Try to have minimum number of bends in routing.
  
  Routing Rules:
  1. Routing first happens on lower metal layers. Once its complete, it does upper layer routing. Ex: First M1 routing happen & then M2 
  2. Via creation happens only during upper layer routing (M2) & not during lower layer routing (M1)

  Processing Routing Guides:
  1. Initial route guides
  2. Splitting (if there are any horizontal guide), if tool is routing on vertical layer
  3. Merging
  4. Bridging
  5. Pre-processed route Guide
  
  ![image](https://user-images.githubusercontent.com/107121814/179302203-653cdb11-7250-48c1-a608-5ab9f6382521.png)  

  Command : run_routing
  ![image](https://user-images.githubusercontent.com/107121814/179303108-1cd643df-8991-4c3d-9c6e-fa776d570abb.png)

  ![image](https://user-images.githubusercontent.com/107121814/179303117-42f5d737-fbd4-4105-8fa6-6953dc32cb2e.png)

  
  Routing completed
  ![image](https://user-images.githubusercontent.com/107121814/179303150-3fb05c43-47bf-4283-a998-46b341fddb8e.png)
  
  Routing results; Files generated after Routing (Final DEF, SPEF)
  ![image](https://user-images.githubusercontent.com/107121814/179303197-bc5336dc-b39b-4ebf-8a66-fc7e286de721.png)


NOTE: If DRC errors persist after routing the user has two options:

  1. Re-run routing with higher QoR settings
  2. Manually fix DRC errors specific in tritonRoute.drc file
  
# SPEF Extraction
  
After routing has been completed interconnect parasitics can be extracted to perform sign-off post-route STA analysis. The parasitics are extracted into a SPEF file. The SPEF extractor is integrated within OpenLANE & SPEF will be generated automatically through the "routing" wrapper

  ![image](https://user-images.githubusercontent.com/107121814/179303867-9489e446-f351-4675-9ad0-266edf4572ee.png)

  ![image](https://user-images.githubusercontent.com/107121814/179303774-2a95edd9-2f4d-4347-8c2c-0b02fe3dfff2.png)
