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
