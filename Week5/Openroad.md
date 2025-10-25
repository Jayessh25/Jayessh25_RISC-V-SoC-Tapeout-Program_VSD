# 🚀 Week 5 :  From RTL to Placement using OpenROAD Flow
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-5-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Week 5** **OpenROAD Project** OpenROAD is an open-source, fully automated RTL-to-GDSII flow for digital integrated circuit (IC) design. It supports synthesis, floorplanning, placement, clock tree synthesis, routing, and final layout generation. OpenROAD enables rapid design iterations, making it ideal for academic research and industry prototyping.

OpenROAD provides OpenROAD-flow-scripts as a native, ready-to-use prototyping and tapeout flow. However, it also enables the creation of any custom flow controllers based on the underlying tools, database and analysis engines. Please refer to the flow documentation here.

OpenROAD-flow-scripts (ORFS) is a fully autonomous, RTL-GDSII flow for rapid architecture and design space exploration, early prediction of QoR and detailed physical design implementation. However, ORFS also enables manual intervention for finer user control of individual flow stages through Tcl commands and Python APIs.

---
## 📑 Table of Contents

1. [Static Behavior Evaluation: CMOS Inverter Robustness – Power Supply Variation](#1-static-behavior-evaluation-cmos-inverter-robustness--power-supply-variation)  
   - [Overview](#overview)  
   - [SPICE Simulation Approach](#spice-simulation-approach)  
   - [Key Observations](#key-observations)  
   - [Simulation Observations: Power Supply Variations](#simulation-observations-power-supply-variations)  
   - [Limitations of Operating at Low Supply Voltages](#limitations-of-operating-at-low-supply-voltages)  
   - [CMOS Inverter Robustness to Device Variations](#cmos-inverter-robustness-to-device-variations)  
   - [Sources of Variation: Oxide Thickness](#sources-of-variation-oxide-thickness)  
2. [Labs](#labs)  
   - [Static Behavior Evaluation: CMOS Inverter Robustness, Power Supply Variation](#static-behavior-evaluation-cmos-inverter-robustness-power-supply-variation)  
   - [Simulation 1 - Inverter Device Variation](#simulation-1---inverter-device-variation)  
     - [Step 1: Navigate to the Design Directory](#step-1-navigate-to-the-design-directory)  
     - [Step 2: Inverter Device Variation Analysis](#step-2-inverter-device-variation-analysis)  
     - [Step 3: View the File Contents](#step-3-view-the-file-contents)  
     - [Step 4: Run the Simulation](#step-4-run-the-simulation)  
   - [Simulation 2 - Inverter Supply Voltage Variation](#simulation-2---inverter-supply-voltage-variation)  
     - [Step 1: Inverter Supply Voltage Variation Analysis](#step-1-inverter-supply-voltage-variation-analysis)  
     - [Step 2: View the File Contents](#step-2-view-the-file-contents)  
     - [Step 3: Run the Simulation](#step-3-run-the-simulation-1)  
     - [Table 1: Power Supply Variation (The Voltage Starvation Study)](#-table-1-power-supply-variation-the-voltage-starvation-study)  
3. [Summary: Day 5 – CMOS Inverter Device and Supply Variation Analysis](#summary-day-5--cmos-inverter-device-and-supply-variation-analysis)  
   - [Objective](#objective)  
   - [Activities Performed](#activities-performed)  
   - [Key Observations and Learning Points](#key-observations-and-learning-points)  
   - [Overall Outcome for Day 5](#overall-outcome-for-day-5)  
4. [Repository Info](#repository-info)

---
##  What is OpenROAD?

**OpenROAD** is an open-source, fully automated **RTL-to-GDSII flow** for digital IC design. It transforms your hardware description into actual silicon layout through synthesis, floorplanning, placement, clock tree synthesis, routing, and final layout generation.

✨ **Why OpenROAD?**
- 🚀 Rapid design iterations
- 🎓 Perfect for academic research
- 🏭 Industry-standard prototyping
- 🆓 Completely open-source

---

##  Understanding ORFS Directory Structure

### 🗂️ Root Level Organization
```plaintext
OpenROAD-flow-scripts/
├── 🐳 docker/          → Docker-based installation & run scripts
├── 📚 docs/            → Complete documentation
├── ⚡ flow/            → Core RTL-to-GDS flow files
├── 🧪 jenkins/         → Regression tests for builds
├── 🛠️ tools/           → Required tools for the flow
├── ⚙️ etc/             → Dependency installers
└── 📌 setup_env.sh     → Environment configuration script
```

### 📁 Inside `flow/` Directory
```plaintext
flow/
├── 🎨 designs/         → Built-in design examples across technology nodes
├── 📝 Makefile         → Automated flow execution
├── 🏭 platforms/       → Technology libraries (LEF, GDS, etc.)
├── 📖 tutorials/       → Learning resources
├── 🔧 util/            → Utility scripts
└── 📜 scripts/         → Flow automation scripts
```

---

# 🚀 OpenROAD Installation & Execution Flow

Welcome to the **OpenROAD Project** — an open-source revolution in digital chip design!  
OpenROAD (Open **Real-time Optimized Autonomous Design**) aims to deliver a **24-hour, no-human-in-the-loop RTL-to-GDSII flow**, empowering designers, researchers, and students to explore ASIC physical design freely.  

This guide provides an overview of how to get started with **OpenROAD**, the core physical design tool, and how it relates to **OpenROAD-Flow-Scripts**, the complete flow automation framework.  

Whether you're a researcher tuning placement algorithms or an engineer running a full ASIC flow — OpenROAD gives you the freedom to explore silicon like never before. 🧠💡

---

## ⚖️ OpenROAD vs OpenROAD-Flow-Scripts

| Feature | **OpenROAD** 🧩 | **OpenROAD-Flow-Scripts** 🧱 |
|:--------|:----------------|:-----------------------------|
| **Purpose** | Core engine for digital physical design | Complete RTL-to-GDSII flow automation |
| **Input** | Netlist (post-synthesis) + LEF/DEF + Liberty | RTL (Verilog) + Config files |
| **Output** | DEF, GDS, timing reports | Full ASIC layout, GDSII, logs, and metrics |
| **Functionality** | Placement, routing, optimization, timing | Integrates multiple tools (Yosys, OpenROAD, OpenSTA, Magic, etc.) |
| **Usage** | Interactive or scripted EDA tool | Makefile-based automated design flow |
| **Flexibility** | Ideal for custom research & module-level design | Ideal for running full SoC or block-level flows |
| **Repository** | [github.com/The-OpenROAD-Project/OpenROAD](https://github.com/The-OpenROAD-Project/OpenROAD) | [github.com/The-OpenROAD-Project/OpenROAD-flow-scripts](https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts) |

---

✨ **In short:**  
- **OpenROAD** = the engine 🧠 that powers the flow.  
- **OpenROAD-Flow-Scripts** = the vehicle 🚗 that drives from RTL to GDSII using that engine.  


### 1. Comments to Installation

Together, they make open-source silicon design faster, smarter, and truly accessible. 🌍💻

> Note:- Before installing This Openroad tool make sure you have installed these dependencies otherwise you will may face much problems

### Required Packages
```bash
sudo apt update
sudo apt install -y build-essential cmake clang bison flex libreadline-dev \
                    gawk tcl-dev tk-dev libffi-dev git graphviz xdot \
                    pkg-config python3 python3-dev libboost-system-dev \
                    libboost-python-dev swig libeigen3-dev
```
Install this tool and make
```bash
git clone https://github.com/google/or-tools.git
cd or-tools
mkdir build && cd build
cmake -DBUILD_DEPS=ON -DCMAKE_BUILD_TYPE=Release ..
make -j$(nproc)
sudo make install
```
Inside your temp folder download and extract this file 

Step 1
```bash
cd /tmp
wget https://github.com/google/or-tools/releases/download/v9.6/or-tools_amd64_ubuntu-22.04_cpp_v9.6.2534.tar.gz
tar -xzf or-tools_amd64_ubuntu-22.04_cpp_v9.6.2534.tar.gz
sudo mv or-tools_Ubuntu-22.04-64bit /usr/local/or-tools
```
Step 2 --Set Environment Variables

```bash
export ortools_DIR=/usr/local/or-tools/lib/cmake/ortools
export CMAKE_PREFIX_PATH=/usr/local/or-tools
echo 'export ortools_DIR=/usr/local/or-tools/lib/cmake/ortools' >> ~/.bashrc
echo 'export CMAKE_PREFIX_PATH=/usr/local/or-tools' >> ~/.bashrc
source ~/.bashrc
```
##### If You will not download these dependeices and file you will may get error like this 

Error No. 1 
```bash
jayesshsk@jayesshsk:/home/jayesshsk/VLSI/OpenROAD# mkdir build && cd build
jayesshsk@jayesshsk:/home/jayesshsk/VLSI/OpenROAD/build# cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-Wno-error" -DCMAKE_PREFIX_PATH="/usr/local" -DCMAKE_CXX_COMPILER=/usr/bin/g++-9
-- The CXX compiler identification is unknown
CMake Error at CMakeLists.txt:54 (project):
  The CMAKE_CXX_COMPILER:

    /usr/bin/g++-9

  is not a full path to an existing compiler tool.

  Tell CMake where to find the compiler by setting either the environment
  variable "CXX" or the CMake cache entry CMAKE_CXX_COMPILER to the full path
  to the compiler, or to the compiler name if it is in the PATH.


-- Configuring incomplete, errors occurred!
jayesshsk@jayesshsk:/home/jayesshsk/VLSI/OpenROAD/build# 

````
Error No. 2 

```bash
- STA library: /home/jayesshsk/VLSI/OpenROAD/build/libOpenSTA.a
-- STA executable: /home/jayesshsk/VLSI/OpenROAD/build/sta
CMake Error at src/gpl/CMakeLists.txt:12 (find_package):
  By not providing "Findortools.cmake" in CMAKE_MODULE_PATH this project has
  asked CMake to find a package configuration file provided by "ortools", but
  CMake did not find one.

  Could not find a package configuration file provided by "ortools" with any
  of the following names:

    ortoolsConfig.cmake
    ortools-config.cmake

  Add the installation prefix of "ortools" to CMAKE_PREFIX_PATH or set
  "ortools_DIR" to a directory containing one of the above files.  If
  "ortools" provides a separate development package or SDK, be sure it has
  been installed.


-- Configuring incomplete, errors occurred!
jayesshsk@jayesshsk:/home/jayesshsk/VLSI/OpenROAD/build# 

```
and when you will run build command you will get fail here so first sure you download these file and make them first 

### **Steps to Install OpenROAD and Run GUI**  

#### **1. Clone the OpenROAD Repository**  
      git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
      cd OpenROAD-flow-script
![screensot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week5/Images/Command1.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week5/Images/Command2.png)
#### **2. Run the Setup Script**  
       sudo ./setup.sh
       
![screensot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week5/Images/Command3.png)


#### **3. Build OpenROAD**  

      ./build_openroad.sh --local

If here you not getting error its fine and good you can skip next some part from cloning again OpenRoad repo and installation of it but i also get here build error so i follows next stpes and i think everyone get here almost IN Below image you will get to know about the error if you also get this error worry not we have second option just follow all steps and you will win

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week5/Images/Command4.png)

If you also get something like as above then follow next steps 

Now again Clone repository of OpenRoad and install it by following these steps 
#### **1. Clone the OpenROAD Repository**  
     git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD.git
     cd OpenROAD

#### **Step 2 — Create a Build Directory**  
      mkdir build
      cd build

#### **Step 3 — Configure and Build**  
      cmake .. -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_CXX_FLAGS="-Wno-error" \
      -DCMAKE_PREFIX_PATH="/usr/local" \
       -DCMAKE_CXX_COMPILER=/usr/bin/g++-9

       make -j$(nproc)
       sudo make install


#### **Step 4 — Verify Installation**
       ls bin/
       ./bin/openroad -version
✅ You should see version output like:
       v2.0-25739-g7319402f48

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week5/Images/Command6.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week5/Images/Command6.png)

Now after this go inside your OpenROAD-flow-script directory which we already cloned in 1 step 
```
 cd OpenROAD-flow-script
```
##### **Step 1 - Connect OpenROAD to Flow-Scripts**

       export OPENROAD_EXE=/home/jayesshsk/VLSI/OpenROAD/build/bin/openroad
       echo 'export OPENROAD_EXE=/home/jayesshsk/VLSI/OpenROAD/build/bin/openroad' >> ~/.bashrc
       source ~/.bashrc
       
##### **Step 2 — Source Environment**

           source ./env.sh
          yosys -help  
          openroad -help

You will see like this 

      
      jayesshsk@jayesshsk:/home/jayesshsk/VLSI/OpenROAD-flow-scripts# source env.sh
       OPENROAD: /home/jayesshsk/VLSI/OpenROAD-flow-scripts/tools/OpenROAD
     jayesshsk@jayesshsk:/home/jayesshsk/VLSI/OpenROAD-flow-scripts# openroad -gui

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week5/Images/Command10.png)

if you find yosys error the do this step otherwise no need

```
export YOSYS_EXE=/usr/local/bin/yosys
echo 'export YOSYS_EXE=/usr/local/bin/yosys' >> ~/.bashrc
source ~/.bashrc
```

##  2. Executing the Flow

With everything built and configured, the next step is to **run the flow** for our design.

### Launching the Flow up to Placement

Inside the flow directory, execute:

```bash
cd flow
make DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk place
```

This single command will:

1. Perform logic synthesis
2. Define the chip outline (floorplanning)
3. Place standard cells based on timing and connectivity

The process stops automatically before clock tree synthesis, as per the target stage (`place`).

After completion, layout files will be available in:

```
results/nangate45/gcd/base/
```
<img width="1210" height="773" alt="Image" src="" />

---

##  3. Visualizing the Physical Layout

The results of the OpenROAD flow can be explored using two visualization tools — **OpenROAD GUI** and **KLayout**.
Before either is used, the environment variables must be set up properly:

```bash
cd ~/OpenROAD-flow-scripts
source ./env.sh
```

### Method 1: OpenROAD GUI

The GUI allows interactive viewing of design data (.odb files).
To open the design:

```bash
cd flow
openroad -gui
```

<img width="1210" height="773" alt="Image" src="" />

Then, through the GUI:

* Navigate to `File → Open`
* Load `results/nangate45/gcd/base/2_floorplan.odb` for the floorplan view

  <img width="1280" height="800" alt="Image" src="" />
  <img width="1210" height="773" alt="Image" src="" />

* Similarly, open `3_place.odb` for the placement visualization
You can explore the die area, cell rows, and standard cell placements within the window.
  
 <img width="1213" height="770" alt="Image" src="" />
 <img width="1210" height="773" alt="Image" src="" />

---

### Method 2: Viewing through KLayout

KLayout works with `.def` format files. Since OpenROAD produces `.odb` outputs, they need conversion.

#### Converting ODB to DEF

```tcl
openroad
read_lef platforms/sky130hd/lef/sky130_fd_sc_hd.tlef
read_lef platforms/sky130hd/lef/sky130_fd_sc_hd_merged.lef
read_db results/nangate45/gcd/base/2_floorplan.odb
write_def results/nangate45/gcd/base/2_floorplan.def
read_db results/nangate45/gcd/base/3_place.odb
write_def results/nangate45/gcd/base/3_place.def
exit
```
#### Verify files are generated

```bash
ls -lh results/nangate45/gcd/base/2_floorplan.def results/nangate45/gcd/base/3_place.def
```

<img width="1213" height="79" alt="Image" src="" />

#### Opening in KLayout

```bash
klayout -l platforms/sky130hd/lef/sky130_fd_sc_hd.tlef -l platforms/sky130hd/lef/sky130_fd_sc_hd_merged.lef results/nangate45/gcd/base/2_floorplan.def
```
This view provides a lightweight alternative to the GUI, excellent for verifying placement alignment and macro geometry.

<img width="1210" height="773" alt="Image" src="" />

---

## 📊 6. Notes and Tips

* Always source `env.sh` when launching a new terminal.
* For missing macros or warnings in KLayout, ensure both `.tlef` and `.lef` files are loaded.
* The `results` folder will contain `.odb`, `.def`, and intermediate report files for analysis.

---

## 🔍 7. Results Snapshot

| Stage         | File Type      | Description                             |
| :------------ | :------------- | :-------------------------------------- |
| Floorplanning | `.odb`, `.def` | Defines die area and block placements   |
| Placement     | `.odb`, `.def` | All standard cells placed and legalized |
| Visualization | `.lef`, `.def` | Used for GUI and KLayout inspection     |

---

## 🎯 8. Final Thoughts

Through this exercise, you completed a **partial OpenROAD physical design flow** — transitioning a design from **RTL to its physical placement** using open-source infrastructure.

This step forms the **foundation of modern chip implementation**, bridging digital logic and real-world geometry.
Each step — synthesis, floorplanning, placement — brings the circuit closer to becoming a tangible silicon design.

By mastering this open-source workflow, you’ve effectively experienced the **first milestone of backend VLSI design automation**.

---


## 🧾 Summary

* Installed and built OpenROAD-flow-scripts
* Resolved dependency issues
* Ran the `gcd` design flow up to placement
* Visualized results in both GUI and KLayout
* Validated the structure and layout geometry successfully

---
---


<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---
