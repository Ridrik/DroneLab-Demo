# Replayer Description

One of DroneLab's core feature is its replayer. It allows the user to select, load, and replay any correctly generated past session, including the same visualization tools as any live session, such as plots, telemetry, 3D trajectories, etc.
This document briefly summarizes how the user can enter in a replay mode, and what to expect from it.

## 1. Run Locator
When the user launches DroneLab, it comes with a base data directory, where it searches for any run within `runs/` folder. By default, it uses the user defined data directory, such as XDG_DATA_HOME on Linux or Local APPDATA on Windows.
This directory can be changed by passing the argument `--data /my/data/dir/path`, to which it will read/write runs on `/my/data/dir/path/runs`.

## 2. Initial state
Head over to `Mission Menu` via the overview panel or main menu settings. Once it opens, you'll see 3 main tabs: a Missions tab, a live run tab, and a run history one. The first one is where you can load, edit and save new missions, which makes use of the Editor panel for all the configurable settings. The 2nd tab is where you can control the session, either in live mode or replay mode, such as starting, pausing, stopping, adjusting speed, or seeing metadata and waypoint summary information. Finally, the 3rd tab displays the saved runs. It's here that the user can see summary and load the run in **replay** mode.   
For runs to appear for replay, the user must first start a session in live mode, via run tab or overview panel. Once it stops - either because of a termination rule, or commanded by user - it should appear in the run history as long as its data is valid (which will be unless some disk fault or software fault occurs, noting that **DroneLab** tries to be resillient and save run state even when the software is prematurely terminated).

## 3. Loading run
Once runs appear on the history, you may select the one of interest. A detail section should appear at the bottom, with metadata summary of the run, such as date, mission id, and actions. If valid, a 'Replay' button should appear at the bottom. Clicking it will load the run, in which the replay session begins. Note that to start a replay, the software must be in a reset state.

## 4. Replay 
Upon loading the replay session, a replay bar panel will apear, which will contain the progress bar, along with navigation buttons. Users may click anywhere on the progress bar to shift the simulation to different time events. The 3D objects will automatically shift to the corresponding positions.   
Click on the play button to have the simulation progressively advance, within the speed that you setup (defaults 1.0x).   
Similarly to a live session, you may see telemetry, plots, trajectory trails and minimap in the corresponding panels.   
To end a replay session, simply click `End Replay` on the overview panel, or on the Missions panel, under the Live session tab.