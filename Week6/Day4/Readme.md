## Day 4 - Pre-layout timing analysis and importance of good clock tree 

### Implementation

* Section 4 tasks:-
1. Fix up small DRC errors and verify the design is ready to be inserted into our flow.
2. Save the finalized layout with custom name and open it.
3. Generate lef from the layout.
4. Copy the newly generated lef and associated required lib files to 'picorv32a' design 'src' directory.
5. Edit 'config.tcl' to change lib file and add the new extra lef into the openlane flow.
6. Run openlane flow synthesis with newly inserted custom inverter cell.
7. Remove/reduce the newly introduced violations with the introduction of custom inverter cell by modifying design parameters.
8. Once synthesis has accepted our custom inverter we can now run floorplan and placement and verify the cell is accepted in PnR flow.
9. Do Post-Synthesis timing analysis with OpenSTA tool.

#### 1. Fix up small DRC errors and verify the design is ready to be inserted into our flow.

Conditions to be verified before moving forward with custom designed cell layout:
* Condition 1: The input and output ports of the standard cell should lie on the intersection of the vertical and horizontal tracks.
* Condition 2: Width of the standard cell should be odd multiples of the horizontal track pitch.
* Condition 3: Height of the standard cell should be even multiples of the vertical track pitch.

Commands to open the custom inverter layout

```bash
# Change directory to vsdstdcelldesign
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign

# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_inv.mag &
```

Screenshot of tracks.info of sky130_fd_sc_hd

![Screenshot from 2024-03-24 13-38-09](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2016-43-52.png)

Commands for tkcon window to set grid as tracks of locali layer

```tcl
# Get syntax for grid command
help grid

# Set grid values accordingly
grid 0.46um 0.34um 0.23um 0.17um
```

Screenshot of commands run

![Screenshot from 2024-03-24 13-49-55](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2016-41-14.png)

Condition 1 verified

![Screenshot](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2016-46-33.png)
![screenshort](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2016-49-35.png)
Condition 2 verified

```math
Horizontal\ track\ pitch = 0.46\ um
```

![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2016-46-33.png)

```math
Width\ of\ standard\ cell = 1.38\ um = 0.46 * 3
```

Condition 3 verified

```math
Vertical\ track\ pitch = 0.34\ um
```

![Screenshot from 2024-03-24 13-58-32](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-02-20.png)
![screenshot](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2016-52-39.png)
```math
Height\ of\ standard\ cell = 2.72\ um = 0.34 * 8
```

#### 2. Save the finalized layout with custom name and open it.

Command for tkcon window to save the layout with custom name

```tcl
# Command to save as
save sky130_vsdinv.mag
```

Command to open the newly saved layout

```bash
# Command to open custom inverter layout in magic
magic -T sky130A.tech sky130_vsdinv.mag &
```

Screenshot of newly saved layout

![Screenshot from 2024-03-24 14-33-20](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2016-41-14.png)

#### 3. Generate lef from the layout.

Command for tkcon window to write lef

```tcl
# lef command
lef write
```

Screenshot of command run

![Screenshot from 2024-03-24 14-35-55](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-04-02.png)

Screenshot of newly created lef file

![Screenshot from 2024-03-24 14-37-19](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-06-04.png)

#### 4. Copy the newly generated lef and associated required lib files to 'picorv32a' design 'src' directory.

Commands to copy necessary files to 'picorv32a' design 'src' directory

```bash
# Copy lef file
cp sky130_vsdinv.lef ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# Copy lib files
cp libs/sky130_fd_sc_hd__* ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/

# List and check whether it's copied
ls ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/
```

Screenshot of commands run

![Screenshot from 2024-03-24 14-55-23](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-08-12.png)

#### 5. Edit 'config.tcl' to change lib file and add the new extra lef into the openlane flow.

Commands to be added to config.tcl to include our custom cell in the openlane flow

```tcl
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```

Edited config.tcl to include the added lef and change library to ones we added in src directory

![Screenshot from 2024-03-24 15-29-56](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-21-54.png)

#### 6. Run openlane flow synthesis with newly inserted custom inverter cell.

Commands to invoke the OpenLANE flow include new lef and perform synthesis 

```bash
# Change directory to openlane flow directory
cd Desktop/work/tools/openlane_working_dir/openlane

# alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
# Since we have aliased the long command to 'docker' we can invoke the OpenLANE flow docker sub-system by just running this command
docker
```
```tcl
# Now that we have entered the OpenLANE flow contained docker sub-system we can invoke the OpenLANE flow in the Interactive mode using the following command
./flow.tcl -interactive

# Now that OpenLANE flow is open we have to input the required packages for proper functionality of the OpenLANE flow
package require openlane 0.9

# Now the OpenLANE flow is ready to run any design and initially we have to prep the design creating some necessary files and directories for running a specific design which in our case is 'picorv32a'
prep -design picorv32a

# Adiitional commands to include newly added lef to openlane flow
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Screenshots of commands run

![Screenshot 6](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-29-10.png)
![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-32-59.png)
![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-50-47.png)

#### 7. Remove/reduce the newly introduced violations with the introduction of custom inverter cell by modifying design parameters.

Noting down current design values generated before modifying parameters to improve timing

![Screenshot](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-32-59.png)
![Screenshot](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-50-47.png)

Commands to view and change parameters to improve timing and run synthesis

```tcl
# Now once again we have to prep design so as to update variables
prep -design picorv32a -tag 24-03_10-03 -overwrite

# Addiitional commands to include newly added lef to openlane flow merged.lef
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Command to display current value of variable SYNTH_STRATEGY
echo $::env(SYNTH_STRATEGY)

# Command to set new value for SYNTH_STRATEGY
set ::env(SYNTH_STRATEGY) "DELAY 3"

# Command to display current value of variable SYNTH_BUFFERING to check whether it's enabled
echo $::env(SYNTH_BUFFERING)

# Command to display current value of variable SYNTH_SIZING
echo $::env(SYNTH_SIZING)

# Command to set new value for SYNTH_SIZING
set ::env(SYNTH_SIZING) 1

# Command to display current value of variable SYNTH_DRIVING_CELL to check whether it's the proper cell or not
echo $::env(SYNTH_DRIVING_CELL)

# Now that the design is prepped and ready, we can run synthesis using following command
run_synthesis
```

Screenshot of merged.lef in `tmp` directory with our custom inverter as macro

![Screenshot from 2024-03-24 23-46-25](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-11-07.png)

Screenshots of commands run

![Screenshot from 2024-03-24 17-10-46](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-21-47.png)

Comparing to previously noted run values area has increased and worst negative slack has become 0

![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-21-43.png)
![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-21-47.png)

#### 8. Once synthesis has accepted our custom inverter we can now run floorplan and placement and verify the cell is accepted in PnR flow.

Now that our custom inverter is properly accepted in synthesis we can now run floorplan using following command

```tcl
# Now we can run floorplan
run_floorplan
```

Screenshots of command run

![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-22-36.png)
![Screenshot](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-25-05.png)

Since we are facing unexpected un-explainable error while using `run_floorplan` command, we can instead use the following set of commands available based on information from `Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl` and also based on `Floorplan Commands` section in `Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md`

```tcl
# Follwing commands are alltogather sourced in "run_floorplan" command
init_floorplan
place_io
tap_decap_or
```

Screenshots of commands run

![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-25-12.png)
![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-25-31.png)
![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-25-45.png)

Now that floorplan is done we can do placement using following command

```tcl
# Now we are ready to run placement
run_placement
```

Screenshots of command run

![Screenshot from 2024-03-24 23-49-29](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-26-11.png)
![Screenshot from 2024-03-24 23-51-08](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-26-48.png)

Commands to load placement def in magic in another terminal

```bash
# Change directory to path containing generated placement def
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/24-03_10-03/results/placement/

# Command to load the placement def in magic tool
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Screenshot of placement def in magic

![Screenshot from 2024-03-25 00-16-54](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-28-28.png)


Command for tkcon window to view internal layers of cells

```tcl
# Command to view internal connectivity layers
expand
```

Abutment of power pins with other cell from library clearly visible

![Screenshot ](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-30-19.png)
![Screenshot](https://github.com/Jaynandan-Kushwaha/silicon-diary/blob/main/Week6/Images/Screenshot%20from%202025-11-11%2017-30-26.png)

