## v0.5.4 - January 2025
### Updated
- Included `fault` library into DroneLab, granting it automatic visibility and traceability against common software faults.

## v0.5.3 - December 2025
### Added
- Exception & signal handling: Users are now properly notified in the (unlikely) case of fatal program termination. Includes visual notification on Windows, also on Linux if user has 'zenity' or 'kdialog' installed.
- Crash reports on exception & signal handling, for data collection and analysis (seen on default data directory on Linux and Windows (XDG_DATA_HOME/DroneLab and LocalAppData/DroneLab, respectively).
### Updated
- Fixed a termination issue that could occur if the program suffered an exception/abnormal signal.
- Fixed an issue related with program normal termination when ros2_interface plugin was enabled (linux only).
- Internal configuration changes.

## v0.5.2 - December 2025
### Fixed
- Resolved issue related to session settings display when on replay sessions.
- Overall performance improvements.

## v0.5.1 - December 2025
### Updated
- Small refactor to data loading for replay sessions, and warning to users.
- New place for notifications and timings.
### Fixed
- Resolved issue when attempting to close the App while replay data was being uploaded. 
- Fixed replay issue on trajectory trails when sessions were long enough.

## v0.5.0 - December 2025
### Added
- Multicopter types: Added hexacopter and octocopter vehicle options. Find it in the `Vehicle` tab on the `Editor` menu.
- Launcher Pad collision mechanism.
### Updated
- ROS2 Interface: Refactored configuration. Apart from topic names, individual QoS reliability and depth are configurable in the user settings panel.

## v0.4.1 - December 2025
### Fixed
- Resolved issue when adding new waypoints interactively after all previous waypoints had been reached.
### Notes
- No new features added

## v0.4.0 - November 2025
### Added
- ROS2 Interface: receive simulator data from the simulator, and send actuator commands when on backend mode.
See `docs/ros2_interface.md` for more. Available on Linux builds.
- AppImage distribution option for Linux builds.
### Updated
- User data (missions, runs, settings, logs) are now pointed to user default locations (e.g APPDATA on Windows, XDG_DATA_HOME on Linux). Previously, they'd point to the working directory.

## v0.3.0 - November 2025
### Added
- New simulator backend mode, using MAVLink UDP connection to send sensor data and receive actuator commands.
See `docs/mavlink_overview.md` for more.
- Updated Actuator/ESC modes with open loop and closed loop options. See `docs/quadcopterX_overview.md` for a summary.
- Other general GUI improvements.

## v0.2.2 – October 2025
- Initial demo release
