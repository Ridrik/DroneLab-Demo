# Replayer Description

One of DroneLab's core feature is its replayer. It allows the user to select, load, and replay any correctly generated past session, including the same visualization tools as any live session, such as plots, telemetry, 3D trajectories, etc.
This document briefly summarizes how the user can enter in a replay mode, and what to expect from it.

## 1. Run Locator
When the user launches DroneLab, it comes with a base data directory, where it searches for any run within runs/ folder. By default, it uses the user defined data directory, such as XDG_DATA_HOME on Linux or APPDATA on Windows.
This directory can be changed by passing the argument `--data /my/data/dir/path`, to which it will read/write runs on `/my/data/dir/path/runs`.

## 2.