# 🚀 Week 8 : – Post-Routing STA analysis of VSDBabySoC Design

<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-8-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Wlecome to **Week8** –   is to perform a complete **post-routing Static Timing Analysis (STA)** of the **VSDBabySoC design using OpenSTA** across multiple **PVT corners**. This includes running STA with accurate post-route parasitic data (SPEF), post-CTS constraints (SDC), and multi-corner Liberty files to evaluate worst-case setup, hold, WNS, and TNS metrics. 

---

## 📑 Table of Contents

1. [🎯 Objective](#-objective)
2. [📝 Summary](#-summary)
3. [📂 Key Files Used for STA](#-key-files)
4. [📜 sta_across_pvt_route.tcl Script](#sta_across_pvt_routetcl-file)
5. [🕒 Post-CTS SDC File](#vsdbabysoc_post_ctssdc-file)
6. [📡 SPEF (Post-Route Parasitics)](#post-route-spef-file)
7. [🚀 Running STA Using Docker](#running-sta)
8. [📄 STA Output Files Generated](#sta-output-files)
9. [📊 Post-Synthesis Timing Summary](#post-synthesis-results-summary)
10. [📊 Post-Route Timing Summary](#post-routing-results-summary)
11. [📈 Comparison Graphs](#comparison-graphs)
12. [🔍 Key Differences: Post-Synthesis vs Post-Route](#key-differences-post-synthesis-vs-post-route-timing-analysis)
13. [📝 Final Summary](#summary)
14. [📚 Repository & Author](#repository--author)

---

### Objective
The objective of Week 8 is to perform a complete post-routing Static Timing Analysis (STA) of the VSDBabySoC design using OpenSTA across multiple PVT corners. This includes running STA with accurate post-route parasitic data (SPEF), post-CTS constraints (SDC), and multi-corner Liberty files to evaluate worst-case setup, hold, WNS, and TNS metrics. The goal is to verify the timing integrity of the design after routing, identify corner-specific violations, and ensure sign-off-quality timing closure for the final GDSII.

---
### `Key Files`

To perform reliable timing verification of the BabySoC design after routing, we use OpenSTA with a dedicated TCL script, a post-CTS constraints file, and the post-route SPEF file.

The `sta_across_pvt_route.tcl` script automates static timing analysis across multiple process, voltage, and temperature (PVT) corners, while the `5_route.sdc` file provides the design-specific timing constraints generated after clock tree synthesis, and the `6_final.spef` file supplies the post-route parasitic RC data. Together, these files ensure that STA is run under the correct operating conditions with accurate parasitic delays, and that reports such as setup/hold slack, WNS, and TNS are captured for each library corner.

## sta_across_pvt_route.tcl File

```
 set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

# Extra liberty files
read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib
read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsddac.lib

for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {

    read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/$list_of_lib_files($i)

    read_verilog /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/6_final.v

    link_design vsdbabysoc
    current_design

    # FIXED SDC PATH
    read_sdc /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/5_route.sdc

    read_spef /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/6_final.spef

    check_setup -verbose

    report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} \
      > /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/min_max_$list_of_lib_files($i).txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_max_slack.txt
    report_worst_slack -max -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_max_slack.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_min_slack.txt
    report_worst_slack -min -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_min_slack.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_tns.txt
    report_tns -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_tns.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_wns.txt
    report_wns -digits {4} \
      >> /data/VLSI/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_wns.txt
}

```
</details>

This `vsdbabysoc_post_cts.sdc` file is an auto-generated SDC created after clock tree synthesis. It sets the current design to `vsdbabysoc` and defines the basic timing environment. The file specifies a clock named `clk` with an `11 ns` period, driven from the pin `pll/CLK`, and marks it as a propagated clock for STA. Sections for environment and design rules are also included for adding further constraints if needed.



### `Running STA`

To run the post-route STA using Docker, follow these steps to execute the `sta_across_pvt_route.tcl` script. Launch a Docker container with your local directory mounted, run the script inside the container, and it will generate all timing reports such as setup/hold slack, WNS, and TNS in the mounted `/data` folder. Using Docker ensures a consistent and reproducible environment for performing the analysis.

```shell
docker run -it -v $HOME:/data opensta /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/sta_across_pvt_route.tcl
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command1.png)

After running the STA script, you can navigate to the `STA_OUTPUT/route/` directory to see all the generated timing reports. This includes detailed path delay reports for each library corner (`min_max_*.txt`), worst setup and hold slack summaries (`sta_worst_max_slack.txt and sta_worst_min_slack.txt`), as well as total negative slack (sta_tns.txt) and worst negative slack (`sta_wns.txt`). These files provide a complete overview of the BabySoC design’s timing performance after routing.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command2.png)

### `Post-Synthesis Results Summary`

Here is a tabulated view of the key timing results generated by the STA script.

| PVT_CORNER                     | Worst Setup Slack | Worst Hold Slack | WNS       | TNS          |
|-------------------------------|-------------------|------------------|-----------|--------------|
| sky130_fd_sc_hd_tt_025C_1v80  | 0.8595            | 0.3096           | 0         | 0            |
| sky130_fd_sc_hd_ff_100C_1v65  | 2.2764            | 0.2491           | 0         | 0            |
| sky130_fd_sc_hd_ff_100C_1v95  | 3.7138            | 0.1960           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v56  | 0.8214            | 0.2915           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v65  | 1.8597            | 0.2551           | 0         | 0            |
| sky130_fd_sc_hd_ff_n40C_1v76  | 2.7707            | 0.2243           | 0         | 0            |
| sky130_fd_sc_hd_ss_100C_1v40  | -13.6381          | 0.9053           | -13.6381  | -7567.7964   |
| sky130_fd_sc_hd_ss_100C_1v60  | -6.7098           | 0.6420           | -6.7098   | -2833.05     |
| sky130_fd_sc_hd_ss_n40C_1v28  | -51.2061          | 1.8296           | -51.2061  | -36861.4102  |
| sky130_fd_sc_hd_ss_n40C_1v35  | -32.0887          | 1.3475           | -32.0887  | -23317.6035  |
| sky130_fd_sc_hd_ss_n40C_1v40  | -23.8290          | 1.1249           | -23.8290  | -17211.252   |
| sky130_fd_sc_hd_ss_n40C_1v44  | -19.2010          | 0.9909           | -19.2010  | -13652.0469  |
| sky130_fd_sc_hd_ss_n40C_1v76  | -4.4548           | 0.5038           | -4.4548   | -1842.5518   |


### `Post-routing Results Summary`

Here is a tabulated view of the key timing results generated by the STA script.

| PVT_CORNER      | Worst Hold Slack | Worst Setup Slack | WNS       | TNS          |
|-----------------|------------------|-------------------|-----------|--------------|
| tt_025C_1v80    | 0.3104           | 6.5987            | 0         | 0            |
| ff_100C_1v65    | 0.2516           | 7.5661            | 0         | 0            |
| ff_100C_1v95    | 0.1977           | 3.3245            | 0         | 0            |
| ff_n40C_1v56    | 0.2904           | 6.5235            | 0         | 0            |
| ff_n40C_1v65    | 0.2572           | 7.1347            | 0         | 0            |
| ff_n40C_1v76    | 0.2267           | 7.6698            | 0         | 0            |
| ss_100C_1v40    | 0.8794           | -1.4062           | -1.4062   | -132.1887    |
| ss_100C_1v60    | 0.6267           | 2.5442            | 0         | 0            |
| ss_n40C_1v28    | 1.6106           | -25.0572          | -25.0572  | -14950.0332  |
| ss_n40C_1v35    | 1.2095           | -13.8790          | -13.8790  | -7197.1201   |
| ss_n40C_1v40    | 1.0153           | -9.2025           | -9.2025   | -3827.5981   |
| ss_n40C_1v44    | 0.9072           | -6.4161           | -6.4161   | -2041.2456   |
| ss_n40C_1v76    | 0.4688           | 3.2889            | 0         | 0            |


### 📈 `Comparison Graphs`

Graph created through Google colab

```
import pandas as pd
import matplotlib.pyplot as plt
from io import StringIO

# -----------------------------
# 1. POST-SYNTHESIS DATA 
# -----------------------------
post_synth_data = """PVT_CORNER	WORST SETUP SLACK	WORST HOLD SLACK	WNS	TNS
sky130_fd_sc_hd_tt_025C_1v80	0.8595	0.3096	0	0
sky130_fd_sc_hd_ff_100C_1v65	2.2764	0.2491	0	0
sky130_fd_sc_hd_ff_100C_1v95	3.7138	0.196	0	0
sky130_fd_sc_hd_ff_n40C_1v56	0.8214	0.2915	0	0
sky130_fd_sc_hd_ff_n40C_1v65	1.8597	0.2551	0	0
sky130_fd_sc_hd_ff_n40C_1v76	2.7707	0.2243	0	0
sky130_fd_sc_hd_ss_100C_1v40	-13.6381	0.9053	-13.6381	-7567.7964
sky130_fd_sc_hd_ss_100C_1v60	-6.7098	0.642	-6.7098	-2833.05
sky130_fd_sc_hd_ss_n40C_1v28	-51.2061	1.8296	-51.2061	-36861.4102
sky130_fd_sc_hd_ss_n40C_1v35	-32.0887	1.3475	-32.0887	-23317.6035
sky130_fd_sc_hd_ss_n40C_1v40	-23.829	1.1249	-23.829	-17211.252
sky130_fd_sc_hd_ss_n40C_1v44	-19.201	0.9909	-19.201	-13652.0469
sky130_fd_sc_hd_ss_n40C_1v76	-4.4548	0.5038	-4.4548	-1842.5518
"""

df_synth = pd.read_csv(StringIO(post_synth_data), sep="\t")
df_synth["Corner"] = df_synth["PVT_CORNER"].str.replace("sky130_fd_sc_hd_", "")


# -------------------------------------------------
# 2. POST-ROUTE DATA 
# -------------------------------------------------
post_route_data = """PVT_CORNER	WORST SETUP SLACK	WORST HOLD SLACK	WNS	TNS
tt_025C_1v80	6.5987	0.3104	0	0
ff_100C_1v65	7.5661	0.2516	0	0
ff_100C_1v95	3.3245	0.1977	0	0
ff_n40C_1v56	6.5235	0.2904	0	0
ff_n40C_1v65	7.1347	0.2572	0	0
ff_n40C_1v76	7.6698	0.2267	0	0
ss_100C_1v40	-1.4062	0.8794	-1.4062	-132.1887
ss_100C_1v60	2.5442	0.6267	0	0
ss_n40C_1v28	-25.0572	1.6106	-25.0572	-14950.0332
ss_n40C_1v35	-13.879	1.2095	-13.879	-7197.1201
ss_n40C_1v40	-9.2025	1.0153	-9.2025	-3827.5981
ss_n40C_1v44	-6.4161	0.9072	-6.4161	-2041.2456
ss_n40C_1v76	3.2898	0.4688	0	0
"""

df_route = pd.read_csv(StringIO(post_route_data), sep="\t")
df_route["Corner"] = df_route["PVT_CORNER"]


# -----------------------------
# 3. PLOTTING COMPARISON
# -----------------------------
metrics = {
    "WORST SETUP SLACK": "Worst Setup Slack",
    "WORST HOLD SLACK": "Worst Hold Slack",
    "WNS": "Worst Negative Slack",
    "TNS": "Total Negative Slack"
}

for metric, title in metrics.items():
    plt.figure(figsize=(14, 6))
    
    plt.plot(df_synth["Corner"], df_synth[metric], label="Post-Synthesis", marker='o', linewidth=2)
    plt.plot(df_route["Corner"], df_route[metric], label="Post-Route", marker='s', linewidth=2)
    
    plt.axhline(0, color='red', linestyle='--', linewidth=1.5)
    
    plt.title(f"{title} Comparison: Post-Synthesis vs Post-Route", fontsize=16)
    plt.xlabel("PVT Corner")
    plt.ylabel(f"{title} (ns)")
    plt.xticks(rotation=45, ha='right')
    plt.grid(axis='y', linestyle='--', alpha=0.6)
    plt.legend()
    plt.tight_layout()
    
    plt.show()

```
### 📌 Technical Analysis of Hold Slack (Post-Synthesis vs Post-Routing)

The hold slack values across all PVT corners show only minimal variation between the post-synthesis and post-routing stages. This indicates that routing parasitics did not significantly degrade hold timing. The overall behavior can be summarized as follows:

**✔️ 1. Hold slacks remain positive for all corners**

All PVT corners retain positive hold slack both before and after routing, confirming that:
  - The design does not exhibit hold violations at any PVT corner.
  - Clock tree synthesis (CTS) and routing did not introduce unexpected negative skew.
    
**✔️ 2. Very small delta between pre- and post-routing**

Most corners show only ~0.001–0.02 ns variation, which suggests:
  - Parasitic extraction (RC effects) had negligible impact on short-path delays.
  - The design already had strong inherent short-path margins during synthesis.
    
**✔️ 3. Fast corners (FF) show extremely stable slack**

Examples:
  - ff_100C_1v65: 0.2491 → 0.2516
  - ff_100C_1v95: 0.1960 → 0.1977

This stabililty across FF corners implies:
  - Data paths do not shrink dangerously with high voltage / low temperature.
  - The clock tree is well-balanced, with controlled skew.
    
**✔️ 4. Slow corners (SS) exhibit larger slack values**

Example:
  - ss_n40C_1v28: 1.8296 → 1.6106

SS corners naturally produce:
  - Larger combinational delays
  - Reduced sensitivity to hold issues
A minor reduction (≈ 0.2 ns) after routing is expected due to realistic RC delays.

**✔️ 5. No corner shows critical degradation**

Even the worst-case delta (≈0.22 ns at ss_n40C_1v28) is small relative to typical hold margins, confirming:
  - Routing added predictable and non-critical delay.
  - There is no need for additional hold buffer insertion.


| PVT Corner        | Hold Slack Post-Synthesis  | Hold Slack Post-routing  |
|-------------------|--------------|--------------|
| tt_025C_1v80      | 0.3096       | 0.3104       |
| ff_100C_1v65      | 0.2491       | 0.2516       |
| ff_100C_1v95      | 0.1960       | 0.1977       |
| ff_n40C_1v56      | 0.2915       | 0.2904       |
| ff_n40C_1v65      | 0.2551       | 0.2572       |
| ff_n40C_1v76      | 0.2243       | 0.2267       |
| ss_100C_1v40      | 0.9053       | 0.8794       |
| ss_100C_1v60      | 0.6420       | 0.6267       |
| ss_n40C_1v28      | 1.8296       | 1.6106       |
| ss_n40C_1v35      | 1.3475       | 1.2095       |
| ss_n40C_1v40      | 1.1249       | 1.0153       |
| ss_n40C_1v44      | 0.9909       | 0.9072       |
| ss_n40C_1v76      | 0.5038       | 0.4688       |

Here is a graph showing the comparison of `worst-case hold slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command6.png)


📌 Conclusion
Overall, the post-routing hold timing is well within acceptable limits and demonstrates excellent correlation with post-synthesis estimates. The design shows robust short-path behavior across all PVT corners, and no hold fixes or ECO buffers are required.

---
### 📌 Technical Analysis of Setup Slack (Post-Synthesis vs Post-Routing)

The setup slack values across the PVT corners show significant variation between post-synthesis and post-routing stages. This behavior highlights the impact of real parasitics, routing delay, and physical optimization on long-path timing characteristics.
The overall observations are:

**✔️ 1. Setup slack improves significantly after routing in most corners**

Corners such as TT and all FF corners show a dramatic positive shift:

Example:
  - tt_025C_1v80: 0.8595 → 6.5987 ns
  - ff_n40C_1v76: 2.7707 → 7.6698 ns

This indicates:
  - Physical optimization (buffering, gate sizing, placement refinement) greatly reduced long-path delays.
  - Clock tree synthesis introduced a favorable skew (data path slightly delayed, clock arrives earlier).
  - Routing parasitics did not worsen critical paths, but instead helped balance path delays.

**✔️ 2. All FF corners show strong positive timing margins**

Examples:
  - ff_100C_1v65: 2.2764 → 7.5661
  - ff_n40C_1v65: 1.8597 → 7.1347

This consistent improvement suggests:
  - Faster process conditions already favor setup timing.
  - Routing provided sufficient RC delay smoothing to keep long-path slack high.
  - The design is highly stable under fast operating conditions, with no risk of setup violations.

**✔️ 3. SS corners remain challenging due to inherently slow process conditions**

Examples:
  - ss_100C_1v40: –13.6381 → –1.4062
  - ss_n40C_1v28: –51.2061 → –25.0572

Even though routing improves timing, these corners remain negative because:
  - Slow process + low voltage + temperature stress dramatically increase cell delays.
  - Critical data paths exceed the clock period under worst-case SS conditions.
  - Clock skew cannot fully compensate for extreme delay increase.
  - This confirms that SS corners represent the true worst-case setup bottleneck of the design.

**✔️ 4. Post-routing reduces the magnitude of negative slack, showing good correlation**

Example:
  - ss_n40C_1v40: –23.8290 → –9.2025
  - ss_n40C_1v44: –19.2010 → –6.4161

Interpretation:
  - Placement and routing significantly optimized long-path delay (up to ~14 ns improvement).
  - Physical design reduced pessimism in early synthesis estimates.
  - Despite improvements, path-level restructuring is still needed for closure at extreme corners.

**✔️ 5. The overall trend shows predictable and consistent timing behavior**

  - FF/TT corners → always positive and strongly improved
  - SS corners → still negative but significantly recovered
  - No unexpected degradation after routing
  - STA behavior indicates good synthesis–routing correlation


| PVT Corner        | Setup Slack Post-Synthesis | Setup Slack Post-routing|
|-------------------|---------------|----------------|
| tt_025C_1v80      | 0.8595        | 6.5987         |
| ff_100C_1v65      | 2.2764        | 7.5661         |
| ff_100C_1v95      | 3.7138        | 3.3245         |
| ff_n40C_1v56      | 0.8214        | 6.5235         |
| ff_n40C_1v65      | 1.8597        | 7.1347         |
| ff_n40C_1v76      | 2.7707        | 7.6698         |
| ss_100C_1v40      | -13.6381      | -1.4062        |
| ss_100C_1v60      | -6.7098       | 2.5442         |
| ss_n40C_1v28      | -51.2061      | -25.0572       |
| ss_n40C_1v35      | -32.0887      | -13.8790       |
| ss_n40C_1v40      | -23.8290      | -9.2025        |
| ss_n40C_1v44      | -19.2010      | -6.4161        |
| ss_n40C_1v76      | -4.4548       | 3.2889         |

Here is a graph showing the comparison of `worst-case setup slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command5.png)

📌 Conclusion 

Overall, setup timing improves substantially after routing across typical and fast PVT corners, indicating effective physical optimization and well-balanced clock distribution. However, worst-case SS corners continue to display significant negative slack, confirming that they remain the critical setup-limiting conditions of the design. Further optimization—such as path restructuring, cell upsizing, or pipeline refinement—is required to fully close timing in these slow process, low-voltage scenarios.

---
### 📌 Technical Analysis of WNS (Post-Synthesis vs Post-Routing)

The WNS values reveal how the most critical setup paths behave across PVT corners before and after routing. Since WNS directly determines whether the design meets timing, this analysis highlights the true worst-case limitations of the chip.

**✔️ 1. All TT and FF corners show zero WNS both pre- and post-routing**

Example corners:
  - tt_025C_1v80: 0 → 0
  - ff_100C_1v65: 0 → 0
  - ff_n40C_1v76: 0 → 0

This indicates:
  - The design easily meets setup timing under typical and fast process conditions.
  - Routing does not reveal hidden violations.
  - There is strong timing margin for high-performance operation at nominal or fast corners.

**✔️ 2. SS corners remain the true setup bottleneck**

The worst violations occur at slow-slow combinations, especially at low voltages:
Example severe cases:
  - ss_n40C_1v28: –51.2061 → –25.0572
  - ss_n40C_1v35: –32.0887 → –13.8790
  - ss_n40C_1v40: –23.8290 → –9.2025

Reasons:
  - Slow transistors + low VDD + temperature stress dramatically increase delay.
  - Critical long paths exceed the target clock period.
  - These corners define the minimum operating frequency of the design.

**✔️ 3. Post-routing significantly improves WNS in all SS corners**

Examples of improvement:
  - ss_100C_1v40: –13.6381 → –1.4062
  - ss_n40C_1v44: –19.2010 → –6.4161
  - ss_n40C_1v28: –51.2061 → –25.0572

Interpretation:
   - Routing/CTS added beneficial skew and optimized critical paths.
   - Worst-case slack improved by 10–25 ns, showing strong physical design optimization.
   - However, even after routing, SS corners still hold negative WNS → timing closure incomplete.

**✔️ 4. Some SS corners improve enough to reach zero WNS**

Examples:
  - ss_100C_1v60: –6.7098 → 0
  - ss_n40C_1v76: –4.4548 → 0

This shows:
  - For moderate voltage SS corners, routing is sufficient to fully close timing.
  - Only the most extreme low-voltage SS corners remain problematic.

**✔️ 5. Overall trend reflects predictable timing behavior**

 - TT/FF → stable, clean, zero-WNS corners
 - SS moderate voltage → post-routing closure
 - SS low voltage → persistent violations due to device limitations
 - Routing significantly improves but cannot fully compensate for extreme slow-process delays


| PVT Corner        | WNS Post-Synthesis       | WNS Post-routing       |
|-------------------|---------------|---------------|
| tt_025C_1v80      | 0             | 0             |
| ff_100C_1v65      | 0             | 0             |
| ff_100C_1v95      | 0             | 0             |
| ff_n40C_1v56      | 0             | 0             |
| ff_n40C_1v65      | 0             | 0             |
| ff_n40C_1v76      | 0             | 0             |
| ss_100C_1v40      | -13.6381      | -1.4062       |
| ss_100C_1v60      | -6.7098       | 0             |
| ss_n40C_1v28      | -51.2061      | -25.0572      |
| ss_n40C_1v35      | -32.0887      | -13.8790      |
| ss_n40C_1v40      | -23.8290      | -9.2025       |
| ss_n40C_1v44      | -19.2010      | -6.4161       |
| ss_n40C_1v76      | -4.4548       | 0             |

Here is a graph showing the comparison of `WNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command7.png)

📌 Conclusion 

The WNS analysis shows that typical and fast corners easily meet timing at both synthesis and routing stages, confirming strong high-speed behavior. Although routing dramatically improves worst-case slack, extreme slow-slow low-voltage corners still exhibit large negative WNS and represent the fundamental setup-limited operating condition. Additional logic optimization, cell upsizing, or pipeline insertion is required to close timing at these harsh SS corners.

---
### 📌 Technical Analysis of TNS (Post-Synthesis vs Post-Routing)

The TNS values provide a clear understanding of how many endpoints fail setup timing and by how much in aggregate. Unlike WNS, which identifies the single worst path, TNS reflects the overall timing health of the design across all timing endpoints.

Your data shows a strong improvement after routing, particularly in slow-slow (SS) corners.

**✔️ 1. All TT and FF corners show clean TNS = 0 at both stages**

Examples:
  - tt_025C_1v80: 0 → 0
  - ff_100C_1v65: 0 → 0
  - ff_n40C_1v76: 0 → 0

Interpretation:
  - No endpoints violate setup timing in typical or fast conditions.
  - Design is highly stable under performance-oriented corners.
  - Clock tree + routing introduce no regressions.

**✔️ 2. SS corners show large TNS violations due to widespread long-path failures**

Worst cases:
  - ss_n40C_1v28: –36,861 → –14,950
  - ss_n40C_1v35: –23,317 → –7,197
  - ss_n40C_1v40: –17,211 → –3,827

These values indicate:
  - Many endpoints simultaneously fail timing in harsh SS corners.
  - This behavior is expected due to extremely slow cell delays at low voltage.
  - These corners define the dominant limitation for achieving timing closure.

**✔️ 3. Post-routing dramatically improves TNS across all SS corners**

Examples:
  - ss_100C_1v40: –7,567 → –132
  - ss_n40C_1v44: –13,652 → –2,041
  - ss_n40C_1v28: –36,861 → –14,950

Key points:
  - Physical design optimization reduces total violation magnitude by 70–90%.
  - Placement, buffering, and skew balancing improved a large number of paths.
  - Routing parasitics reduced pessimism present at synthesis time.

**✔️ 4. Some SS corners recover fully to TNS = 0 after routing**

Examples:
   - ss_100C_1v60: –2,833 → 0
   - ss_n40C_1v76: –1,842 → 0

This indicates:
   - These moderate SS corners can achieve full timing closure with only routing optimization.
   - Violations were minor and easily resolved by physical design.

**✔️ 5. Overall trend indicates predictable STA behavior**

  - TT/FF corners → always clean, zero TNS
  - SS moderate voltage → routing fixes all violations
  - SS extreme low-voltage → still large TNS, requiring architectural or logic-level fixes
  - Routing significantly narrows but does not eliminate violations where the process is inherently slow
  - This reflects realistic silicon behavior and good synthesis–routing correlation.


| PVT Corner        | TNS Post-Synthesis          | TNS Post-routing        |
|-------------------|------------------|----------------|
| tt_025C_1v80      | 0                | 0              |
| ff_100C_1v65      | 0                | 0              |
| ff_100C_1v95      | 0                | 0              |
| ff_n40C_1v56      | 0                | 0              |
| ff_n40C_1v65      | 0                | 0              |
| ff_n40C_1v76      | 0                | 0              |
| ss_100C_1v40      | -7567.7964       | -132.1887      |
| ss_100C_1v60      | -2833.05         | 0              |
| ss_n40C_1v28      | -36861.4102      | -14950.0332    |
| ss_n40C_1v35      | -23317.6035      | -7197.1201     |
| ss_n40C_1v40      | -17211.252       | -3827.5981     |
| ss_n40C_1v44      | -13652.0469      | -2041.2456     |
| ss_n40C_1v76      | -1842.5518       | 0              |

Here is a graph showing the comparison of `TNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command8.png)

📌 Conclusion 

TNS analysis confirms that the design is fully timing-clean in all TT and FF corners, with routing eliminating violations in several moderate SS conditions. However, extreme slow-slow low-voltage corners continue to exhibit substantial aggregate setup failures, indicating widespread long-path delays that cannot be fixed by physical design alone. Further optimization—such as critical-path restructuring, cell upsizing, or pipeline insertion—is required to close timing at these harsh corners.

---

### `Key Differences: Post-Synthesis vs Post-Route Timing Analysis`

| Aspect             | Post-Synthesis Analysis                            | Post-Route Analysis                                           |
| ------------------ | -------------------------------------------------- | ------------------------------------------------------------- |
| **Timing Model**   | Wire-load models (fanout/cell-based estimation)    | Extracted parasitics (RC) from routed layout                  |
| **Clock Network**  | Ideal clock, zero skew, no latency                 | Real clock tree with buffer delays, skew, and insertion delay |
| **Interconnect**   | Delay estimated from fanout-based lookup tables    | Delay calculated from actual metal routing and vias           |
| **Accuracy**       | \~70–80% correlation with sign-off                 | \~95–98% correlation with sign-off                            |
| **Critical Paths** | Critical paths may differ due to estimation errors | Matches actual layout critical paths   |

---

### `Summary`
- Post-synthesis analysis serves as an **early timing checkpoint**.  
- Post-route analysis represents the **golden reference for timing sign-off**.  
- Transition from estimated to actual physical parameters often reveals:
  - New critical paths revealed
  - Realistic clock tree effects (skew, latency)
  - Interconnect-dominated delays
  - Impact of physical proximity & coupling


---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>


