# DroneLab - 3D simulator and visualizer tool for drone guidance, navigation, control (GNC)

**Platform:** Windows 10/11 (64-bit), Linux (tested on Ubuntu 22.04, AppImage/.tar.gz packages)   
**Developer:** Rodrigo Rosa  

---

## 🌐 Project Showcase & Live Media

See images, animations, and videos of DroneLab in action on the public showcase:  

🔗 **Live Showcase:** [https://ridrik.github.io/DroneLabShowcase/]  
💾 **GitHub Page:** [https://github.com/Ridrik/DroneLabShowcase]  

---

## Overview

**DroneLab** is a 3D drone visualization and simulation environment developed in **C++**, using **OpenGL** for rendering and **Dear ImGui** for its real-time interface.  
It focuses on **Guidance, Navigation, and Control (GNC)** — providing a lightweight yet expressive tool for visualizing flight trajectories, telemetry, and control algorithms in an interactive 3D space.

---

### Key Features
- Main multicopter vehicle types available with configurable mixer allocation
- Waypoint following with configurable parameters  
- Real-time visualization of flight data, via telemetry, plots, trails and more.
- Replay and telemetry visualization  
- Adjustable Flight and GNC parameters, editable and loadable missions.
- Lightweight and intuitive interface  
- (Experimental) MAVLink and ROS2 Interfaces, including simulation backend mode where it accepts actuator commands

---

### Author's Note / Purpose
The idea behind this software was to build a simulation tool that can provide users with easier experimentation capabilities related with UAVs, such as configurability of scenarios, visualization of data, and replayability, as well as external interfaces that enable custom external flight controllers.   
The purpose of this simulator is to eventually serve as both a tool for educational level (undergrad/grad), as well as for experimental purposes at research level or institutional level, as features and refinements are made.


---

## 💻 System Requirements

| Requirement | Description |
|--------------|-------------|
| **OS** | Windows 10 or 11 (64-bit), Linux (tested on Ubuntu 22.04; AppImage/.tar.gz packages) |
| **GPU** | OpenGL 3.3+ compatible |
| **Storage** | ~40-50 MB free space |
| **Memory** | 4 GB RAM recommended |

---

## Installation

1. Download the latest installer from the [Releases page](https://github.com/ridrik/DroneLab-Demo/releases/latest).
2. Follow the on-screen instructions.  
3. Launch DroneLab executable from installed folder.

---

## File Locations
- Missions: `$DATA_DIR/missions/` (`.json` configs)  
- Run data: `$DATA_DIR/runs/` (includes `.json` summaries and `.csv` flight data)
- `$DATA_DIR` defaults to Local APPDATA on Windows or XDG_DATA_HOME on Linux, unless overriden by user (`--data /my/data/dir`)

---

## 🧩 Third-Party Components and Licenses

**DroneLab** makes use of several open-source libraries and assets, included in compiled form within this software.  
These components are redistributed under their respective licenses.  
No source modifications have been made unless otherwise noted.

| Component | Purpose | License |
| ---------- | -------- | -------- |
| [**Eigen**](https://gitlab.com/libeigen/eigen) | Linear algebra (matrix and vector math) | MPL 2.0 |
| [**spdlog**](https://github.com/gabime/spdlog) | Fast C++ logging library | MIT |
| [**Assimp**](https://github.com/assimp/assimp) | Import/export library for 3D model formats | BSD-3-Clause |
| [**Boost**](https://www.boost.org/) | General-purpose C++ libraries | Boost Software License 1.0 |
| [**GLFW**](https://www.glfw.org/) | OpenGL window/context and input management | zlib/libpng |
| [**OpenGL**](https://www.khronos.org/opengl/) | Cross-platform graphics API | Khronos OpenGL license |
| [**Dear ImGui**](https://github.com/ocornut/imgui) | Immediate Mode GUI library | MIT |
| [**ImPlot**](https://github.com/epezent/implot) | Plotting library for ImGui | MIT |
| [**nlohmann/json**](https://github.com/nlohmann/json) | JSON configuration and mission handling | MIT |
| [**fault**](https://github.com/Ridrik/fault) | Crash handler and Panic library | MIT |
| [**cpptrace**](https://github.com/jeremy-rifkin/cpptrace) | Lightweight stack trace and debugging helper | MIT |
| [**GLAD**](https://glad.dav1d.de/) | OpenGL function loader | MIT |
| [**FreeType**](https://gitlab.freedesktop.org/freetype/freetype) | Font rendering engine | FreeType License |
| [**stb_image**](https://github.com/nothings/stb) | Image loading for textures | Public Domain / MIT |

---

### Fonts and Assets

| Asset | Source | License |
| ------ | ------- | -------- |
| **Noto Sans** | [Google Fonts](https://fonts.google.com/specimen/Noto+Sans) | SIL Open Font License 1.1 |
| **Font Awesome** | [fontawesome.com](https://fontawesome.com) | SIL Open Font License 1.1 |
| **Textures** | [ambientCG.com](https://ambientcg.com) | CC0 1.0 Universal (Public Domain) |

All included fonts and textures retain their original licenses and attribution notices.  
3D models were created in-house using Blender, with no third-party commercial assets.

---

### Acknowledgment

This software would not be possible without the contributions of the open-source community.  
All trademarks and copyrights belong to their respective owners.

---

## Disclaimer

This demo software is provided **for educational and research purposes only**.  
It is distributed **as-is**, without warranty or liability.  
Use at your own discretion.

---

## Contact
Email: rodrigo.csrosa@proton.me   
LinkedIn: https://www.linkedin.com/in/rodrigo-rosa-4547b115b/

---