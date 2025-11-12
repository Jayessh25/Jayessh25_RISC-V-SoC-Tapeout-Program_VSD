# 🚀 Week 6 :  Sky130 Day 2 – Floorplanning Fundamentals LABS
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-6-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Wlecome to **Day 2** – **Floorplanning Fundamentals**, the focus was on understanding and implementing the floorplanning stage of the ASIC design flow using the **Sky130 PDK** within the **OpenLANE environment**. The process involved defining key physical parameters such as die area, core utilization, and aspect ratio, followed by setting up I/O pin placements and establishing a robust power distribution network (PDN) with proper tap cell insertion. Using OpenLANE configuration switches, the floorplan was customized and optimized for design efficiency.

---

## 📑 Table of Contents

1. [🎯 Objective](#-objective)
2. [1️⃣ Floorplan Fundamentals](#1-floorplan-fundamentals)
   - [🗺️ What is Floorplanning?](#️-what-is-floorplanning)
   - [🎯 Floorplan Quality Metrics](#-floorplan-quality-metrics)
   - [✅ Good vs Bad Floorplan](#-good-vs-bad-floorplan)
   - [⚠️ Floorplan Red Flags](#️-floorplan-red-flags)
3. [2️⃣ Floorplan Configuration & Execution](#2-floorplan-configuration--execution)
   - [🎛️ Essential Floorplan Switches](#️-essential-floorplan-switches)
   - [📊 Configuration Guidelines](#-configuration-guidelines)
   - [🚀 Running Floorplan in OpenLANE](#-running-floorplan-in-openlane)
   - [📐 Area Calculation](#area-calculation)
4. [3️⃣ Analyzing and Visualizing Floorplan Results](#3-analyzing-and-visualizing-floorplan-results)
   - [📊 Floorplan Results Analysis](#-floorplan-results-analysis)
   - [🧩 I/O Placer Log Analysis](#-io-placer-log-analysis)
   - [📂 DEF File (Design Exchange Format)](#-def-file-design-exchange-format)
   - [📈 Key Metrics Extraction](#-key-metrics-extraction)
   - [🎨 Visualizing with MAGIC Layout Viewer](#-visualizing-with-magic-layout-viewer)
   - [🔍 Critical Areas to Inspect](#-critical-areas-to-inspect)
5. [4️⃣ Placement Commands](#placement-commands)
   - [📂 Loading the .def File into Magic](#loading-the-def-file-into-magic)
   - [✅ Quality Checks](#-quality-checks)
   - [⚠️ Common Issues and Fixes](#️-common-issues-and-fixes)
6. [💡 Key Takeaways](#-key-takeaways)
7. [📚 Repository & Author](#-repository--author)
   
---

## Objective
The objective of **Week 6 – Sky130 Day 2: Floorplanning Fundamentals** is to understand and implement the **floorplanning stage** of the ASIC physical design flow using **OpenLANE** and the **Sky130 PDK**.  
This phase defines the **die and core dimensions**, **I/O pin placement**, **power distribution network (PDN)**, and **macro planning** — laying the groundwork for successful placement and routing.  
Through hands-on OpenLANE commands and visualization in **Magic Layout**, we aim to:
- Configure and execute the floorplan step from the synthesis output.  
- Analyze DEF and log files to extract chip dimensions, utilization, and aspect ratio.  
- Verify correctness via **I/O pin placement**, **power rings**, and **tap cell insertion**.  
- Build a strong foundation for the **placement** and **routing** stages of the ASIC design flow.  

---

## 1. Floorplan Fundamentals

### 🗺️ What is Floorplanning?

Floorplanning is the foundation of physical design where we:
- 📐 Define chip dimensions (die area and core area)
- ⚡ Plan power distribution networks (PDN)
- 📍 Place I/O pins strategically
- 🎯 Reserve areas for macros and standard cells

> 💡 **Think of it this way:** If your chip is a city, floorplanning is urban planning—deciding where roads, power lines, and buildings go before construction begins!

### 🎯 Floorplan Quality Metrics

```mermaid
graph TD
    A[Floorplan Quality] --> B[Area Metrics]
    A --> C[Power Planning]
    A --> D[I/O Strategy]
    A --> E[Routability]
    
    B --> B1[Utilization: 50-70%]
    B --> B2[Aspect Ratio: ~1.0]
    
    C --> C1[Core Ring]
    C --> C2[Power Stripes]
    
    D --> D1[Strategic Placement]
    D --> D2[Minimize Wire Length]
    
    E --> E1[Routing Channels]
    E --> E2[No Congestion]
```

**Key Formulas:**

```
Utilization Factor = (Area occupied by cells / Total core area) × 100%

Aspect Ratio = Height of Core / Width of Core

Good Floorplan = {Utilization: 50-70%, Aspect Ratio: 0.8-1.2, No congestion}
```

---

### ✅ Good vs Bad Floorplan

| Aspect | ✅ Good Floorplan | ❌ Bad Floorplan |
|--------|------------------|------------------|
| **📏 Utilization** | 50-70% (routing space available) | >80% (no routing space) |
| **📐 Aspect Ratio** | 0.8-1.2 (balanced) | >2.0 or <0.5 (timing skew) |
| **🔌 I/O Placement** | Strategic, near related logic | Random, crisscross connections |
| **⚡ Power Grid** | Core ring + adequate stripes (~150µm pitch) | Sparse, large pitch (IR drop) |
| **🎯 Macro Placement** | Based on connectivity | Blocking routing channels |
| **🛣️ Routing Channels** | Adequate space between rows | Congestion hotspots |
| **📦 Core Margin** | 2-5µm from die edge | Cells touching edge |

### ⚠️ Floorplan Red Flags

```
🚨 DANGER SIGNS:
   ├── Utilization > 85% → Routing nightmare ahead!
   ├── Aspect Ratio > 2.0 → Timing closure impossible
   ├── Overlapping macros → Physical violation
   ├── IR drop > 10% → Reliability failure
   └── Congestion > 80% → Design will not route
```

---

## 2. Floorplan Configuration & Execution

### 🎛️ Essential Floorplan Switches

#### Core Configuration Parameters

| Switch | Purpose | Typical Value | Impact if Wrong |
|--------|---------|---------------|-----------------|
| **🔲 `FP_CORE_UTIL`** | Core area filled with cells | **50-60%** | Too high = No routing space<br>Too low = Wasted area |
| **🟪 `FP_ASPECT_RATIO`** | Core height/width ratio | **1.0 (square)** | Extreme values = Timing skew |
| **⚖️ `FP_SIZING`** | Relative (auto) or absolute sizing | `"relative"` | Wrong mode = Area mismatch |
| **📐 `DIE_AREA`** | Die boundary coordinates | `"0 0 1000 1000"` | Only with absolute sizing |

#### I/O Configuration

| Switch | Purpose | Typical Value | Impact if Wrong |
|--------|---------|---------------|-----------------|
| **🔌 `FP_IO_MODE`** | I/O pin placement pattern | `0` = matching<br>`1` = random | Poor timing, access issues |
| **🧭 `FP_IO_HMETAL`** | Horizontal I/O metal layer | **4** (Metal4) | DRC, routing issues |
| **🧭 `FP_IO_VMETAL`** | Vertical I/O metal layer | **3** (Metal3) | Layer mismatch |
| **🧱 `FP_CORE_MARGIN`** | Core to die edge spacing | **2-5 µm** | Too small = No power ring |

#### Power Planning

| Switch | Purpose | Typical Value | Impact if Wrong |
|--------|---------|---------------|-----------------|
| **🔋 `FP_PDN_CORE_RING`** | Enable power ring around core | `1` = Enable | No ring = IR drop issues |
| **♻️ `FP_PDN_AUTO_ADJUST`** | Auto-adjust PDN to fit | `1` = Enable | Manual fixes needed |
| **📏 `FP_PDN_VPITCH`** | Vertical power stripe spacing | **≈150 µm** | IR drop or blockage |
| **📏 `FP_PDN_HPITCH`** | Horizontal power stripe spacing | **≈150 µm** | IR drop or blockage |
| **⚡ `VDD_NETS`** | Power net name(s) | `"vccd1"` or `"VDD"` | Power not connected |
| **⚡ `GND_NETS`** | Ground net name(s) | `"vssd1"` or `"GND"` | Ground not connected |

#### Advanced Configuration

| Switch | Purpose | Typical Value | Impact if Wrong |
|--------|---------|---------------|-----------------|
| **⚙️ `FP_TAPCELL_DIST`** | Distance between tap cells | **14 µm** | Latch-up risk |
| **🧠 `DESIGN_IS_CORE`** | Core or macro block | `1` = Core | Wrong PDN structure |

---

### 📊 Configuration Guidelines

**Utilization Strategy:**
```
├── 40-50% → Low density, easy routing (test chips)
├── 50-60% → Balanced (RECOMMENDED START)
├── 60-70% → High density, careful routing
├── 70-80% → Very tight, expert-level
└── >80%  → DANGER ZONE! Routing failure likely
```

**Aspect Ratio Effects:**
```
├── 0.5 → Very wide (2:1) → Horizontal wire bias
├── 1.0 → Square ✅ → Balanced timing
├── 2.0 → Very tall (1:2) → Vertical wire bias
└── > 2.5 → Extreme! → Severe timing skew
```

---

### 🚀 Running Floorplan in OpenLANE
# Floorplan Commands

Inorder to carry out the floorplan, we need to continue from the synthesis step in OpenLane flow

```bash
cd openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
run_floorplan
```
![Floorplan Run](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment2.png)
![Floorplan Success](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment2.1.png)

## Area Calculation 
To calculate the area in microns we take dimensions from `/reults/floorplan/picorv32a.floorplan.def` directory in runs folder.
![Area Calculation](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment4.2.png)

In the above image, we can decode that 
- 1000 units = 1 Microns
- Die width = 660685-0 = 660685
- Die lenght = 671405-0 = 671405
- Distance in Microns = Value in Unit Distance/1000
- Die Width in Microns = 660685/1000 = 660.685 Microns
- Die Length in Microns = 671405/1000 = 671.405 Microns
- Area of die in Microns = 660.685 x 671.405 = 443587.212425 sq.microns

To view the floorplan on magic, go into the runs command of your design file and go to floorplan and use the command

**What's happening:**
- ✅ OpenLANE executing floorplan stages
- 📊 I/O placement, tap cell insertion, PDN generation
- ⚡ Die dimensions calculated based on configuration
- 🎯 Standard cell rows created
- ⏱️ Execution time displayed for each sub-step

**Success Indicators:**
- No errors in log
- Die area calculated successfully
- I/O pins placed without violations
- Tap cells inserted properly

---

## 3. Analyzing and Visualizing Floorplan Results

### 📊 Floorplan Results Analysis

#### Default Configuration Reference

**Location:** `$OPENLANE_ROOT/configuration/floorplan.tcl`

![Default Floorplan Config](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment1.3.png)

**Contains:**
- 📋 Default values for all switches
- 🔧 Core utilization, aspect ratio defaults
- ⚡ PDN configuration defaults
- 🔌 I/O placement defaults
- 🎯 Fallback values if not overridden

---

#### I/O Placer Log Analysis

**Location:** `logs/floorplan/ioPlacer.log`

![IO Placer Log](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment3.1.png)

**Information provided:**
- 🔌 I/O pin placement algorithm details
- 📍 Pin distribution around die
- ⚙️ I/O placement mode used
- 🧭 Metal layers assigned to pins
- ✅ Verification of successful placement

**Check for:**
- All I/O pins accounted
- No placement violations
- Correct metal layers
- Adequate pin spacing

---

#### DEF File (Design Exchange Format)

**Location:** `results/floorplan/picorv32a.floorplan.def`

![DEF File](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment4.2.png)

**Key Information:**

| Element | Description | Format |
|---------|-------------|--------|
| **DIEAREA** | Die boundary coordinates | `(llx lly) (urx ury)` |
| **UNITS** | Database unit definition | `DISTANCE MICRONS 1000` |
| **COMPONENTS** | Placed instances count | Number of cells |
| **PINS** | I/O pin count | Number with coordinates |
| **ROWS** | Standard cell row definitions | Origin, orientation, spacing |

**Verification checklist:**
- Die area matches expectations
- Core area has proper margin
- All I/O pins present with valid coordinates
- Row definitions correct

---

### 📈 Key Metrics Extraction

```
📊 Floorplan Quality Metrics:
   ├── 📐 Die Area (µm²) = Width × Height
   ├── 📦 Core Area (µm²) = Area for cells
   ├── 🎯 Utilization (%) = (Cell area / Core area) × 100
   ├── 📏 Aspect Ratio = Core Height / Core Width
   ├── 🔌 Number of I/O pins
   ├── ⚡ Power grid pitch (µm)
   └── 🧱 Core margin (µm)
```

**Calculation from DEF:**
```python
DIE_WIDTH = (urx - llx) / 1000    # Convert to microns
DIE_HEIGHT = (ury - lly) / 1000
DIE_AREA = DIE_WIDTH * DIE_HEIGHT
ASPECT_RATIO = CORE_HEIGHT / CORE_WIDTH
UTILIZATION = (TOTAL_CELL_AREA / CORE_AREA) * 100
```

---

### 🎨 Visualizing with MAGIC Layout Viewer

#### Opening the Floorplan

Navigate to results and launch MAGIC:

```bash
# Change directory to path containing generated floorplan def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-10_10-22/results/floorplan/

# Command to load the floorplan def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

![Floorplan ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment5.0.png)

---

#### Full Chip View

In the above command we are linking the sky130A technology file, reading the merge file we created before synthesis and reading the .def file generated during floorplan stage. 

The output opened on magic will look like this 
![Floorplan output](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment5.1.png)

We can zoom in and look at the i/o placements 
![IO Pads](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment5.2.png)

**Visual Elements:**

| Color/Element | Represents | Significance |
|---------------|-----------|--------------|
| 🟦 **Blue/Purple** | Standard cell placement regions | Where cells will be placed |
| 🟥 **Red lines** | Metal layers (routing) | Available routing resources |
| 🟨 **Yellow rectangles** | I/O pins at periphery | External connection points |
| ⚪ **White space** | Routing channels | Critical for successful routing |
| 🎯 **Grid pattern** | Placement sites | Legal cell positions |
| **Black outline** | Die boundary | Physical chip edge |

**Navigation:**
- **Center design:** Press `S` (select all) then `V` (view all)
- **Zoom in:** Left-click & drag to select, press `Z`
- **Zoom out:** Press `Shift + Z`
- **Return to full view:** Press `V`

---

#### Essential MAGIC Commands

**Basic Navigation:**

| Command | Action | Usage |
|---------|--------|-------|
| **`S`** | Select | Click, then `S` |
| **`V`** | View All | Centers entire design |
| **`Z`** | Zoom In | Select area + `Z` |
| **`Shift+Z`** | Zoom Out | Zoom out one level |
| **`U`** | Undo | Undo last action |

**Inspection Commands:**

| Command | Action | Result |
|---------|--------|--------|
| **`what`** | Query object | Cell name, type, layer info in tkcon |
| **`:box`** | Show dimensions | Box coordinates & dimensions |
| **`I`** | Select instance | Selects entire cell |
| **`B`** | Bounding box | Shows cell boundaries |
| **`X`** | Expand hierarchy | Shows internal structure |

**Display Commands:**

| Command | Purpose |
|---------|---------|
| **`?`** | Show help menu |
| **`G`** | Toggle grid display |
| **`;`** | Open command line |

---

#### Detailed Cell Inspection

![MAGIC What View](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment5.2.png)

**Achieved by:**
1. Zoom into region of interest
2. Hover cursor over cell
3. Press `S` to select
4. Type `what` in tkcon window
5. Information appears below

**Displays:**
- Cell name (decap, tap, filler)
- Layer information (Metal1, Metal2, etc.)
- Cell boundaries and dimensions
- Pin locations

**Verification:**
- ✅ Cells aligned to grid
- ✅ No overlapping cells
- ✅ Proper cell type
- ✅ Correct layer usage

---

### 🔍 Critical Areas to Inspect

```
🔍 Inspection Checklist:
   ├── 🔌 I/O Pins → Location, metal layer, size
   ├── 🎯 Core Boundary → Margin from edge, proper rows
   ├── ⚡ Power/Ground Rings → Width, layers, connectivity
   ├── 🧱 Tap Cells → Spacing (~14µm), row placement
   ├── 📍 Macros (if any) → Position, orientation, blockages
   └── 🟦 Standard Cell Rows → Height, spacing, continuity
```
---
# Placement Commands

Inorder to carry out the placement, we need to continue from the floorplan step in OpenLane flow. The placement step performs congestion aware placement. 

```bash
cd openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
run_floorplan
run_placement
```
![Run_Placement](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment6%20run_placement.png)
![Placement Success](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment6.1.png)

## Loading the .def file into magic
To view the floorplan on magic, go into the runs command of your design file and go to floorplan and use the command

```bash
# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/27-10_10-22/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

In the above command we are linking the sky130A technology file, reading the merge file we created before synthesis and reading the .def file generated during floorplan stage. 

The output of the placement process looks like the below image 
![Placement](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment6.2.png)

Upon zooming in we can view the standard cells used 
![Standard cells](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day2/Images/Comment6.3.png)

---

### ✅ Quality Checks

**Dimensional Verification:**
- Die dimensions match specifications
- Core utilization 50-70%
- Aspect ratio reasonable (~1.0)

**I/O Verification:**
- All pins placed successfully
- Logical pin locations
- No congestion
- Correct metal layers

**Power Planning:**
- Core ring present (if enabled)
- Adequate power stripe pitch
- VDD/GND nets connected
- No PDN DRC violations

**Placement Readiness:**
- Standard cell rows defined correctly
- Adequate routing channels
- No blockage overlaps
- Tap cells fit properly

---

### ⚠️ Common Issues and Fixes

| Issue | Symptom | Fix |
|-------|---------|-----|
| **High Utilization** | >80% usage | Increase die size or reduce `FP_CORE_UTIL` |
| **No Core Margin** | Cells at die edge | Set `FP_CORE_MARGIN` properly |
| **I/O Pin Overlap** | Pins too close | Adjust I/O mode or placement |
| **Missing Power Ring** | No ring visible | Enable `FP_PDN_CORE_RING` |
| **Wrong Aspect Ratio** | Very tall/wide | Adjust `FP_ASPECT_RATIO` |

---

## 💡 Key Takeaways

- 🎯 Good floorplan balances utilization (50-70%), aspect ratio (~1.0), and routability
- 📐 Configuration switches control critical parameters: core utilization, I/O placement, power planning
- 🔍 Analysis requires checking DEF files, logs, and visual inspection in MAGIC
- ⚡ Power planning must include core rings and adequate stripe spacing
- ✅ Verification before placement prevents costly downstream issues

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
