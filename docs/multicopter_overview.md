# Multicopter overview

This document describes the 3 vehicle types simulated by DroneLab, as well as the expected command interface on backend mode via MAVLink.
The simulated types are the quadcopter, hexacopter and octocopter. These use, respectively, 4, 6 and 8 equidistanced motors around the body, connected via arms to the chassis.
All vehicle types are modelled in **body NED coordinates**:

- **+X** → forward  
- **+Y** → right  
- **+Z** → down  

With motors arranged diagonally around the center of mass, in equal angle distances.

---

## 1. Quadcopter Actuator Model (X Frame Configuration)

### Motor Indexing and Rotation Directions

Motor indices follow the simulator’s **actuator order 0–3**, all normalized 0–1. The actual order and orientation is configurable
in the GUI Editor, under the `Mixer` tab. The default configuration is as follows:

            Front (+X)
                ^
                |
    (2) CW         (0) CCW
        \             /
         \           /
          \         /
           \       /
            -------
           /       \
          /         \
         /           \
    (1) CCW          (3) CW
                |
                v
            Back (-X)


| Motor Index | Position      | Rotation |
|-------------|----------------|----------|
| **0**       | Front Right    | CCW       |
| **1**       | Rear Left      | CCW       |
| **2**       | Front Left     | CW      |
| **3**       | Rear Right     | CW      |

This arrangement ensures that opposite motors spin in the same direction for torque balance.

---

## 2. Hexacopter Actuator Model

The hexacopter divides its motors in equal spaced angles of 60º between each other, with motors arranged symmetrically around the center of mass.

### Motor Indexing and Rotation Directions
Motor indices follow the simulator’s **actuator order 0–5**, all normalized 0–1. The default configuration is as follows:

| Motor Index | Position         | Rotation |
|-------------|------------------|----------|
| **0**       | Front 30° Right  | CCW      |
| **1**       | Rear 30° Right   | CW       |
| **2**       | Front 30° Left   | CCW       |
| **3**       | Left             | CC      |
| **4**       | Rear 30° Left    | CCW      |
| **5**       | Right    | CW       |

## 3. Octocopter Actuator Model

The octocopter divides its motors in equal spaced angles of 30º between each other, with motors arranged symmetrically around the center of mass.

### Motor Indexing and Rotation Directions
Motor indices follow the simulator’s **actuator order 0–7**, all normalized 0–1. The default configuration is as follows:

| Motor Index | Position            | Rotation |
|-------------|---------------------|----------|
| **0**       | Front 22.5° Right   | CCW      |
| **1**       | Front 67.5° Right   | CW       |
| **2**       | Front 67.5° Left    | CCW      |
| **3**       | Front 22.5° Left    | CW       |
| **4**       | Rear 22.5° Left     | CCW      |
| **5**       | Rear 22.5° Right    | CW       |
| **6**       | Rear 67.5° Left     | CCW      |
| **7**       | Rear 67.5° Right    | CW       |

## 4. Backend Actuator Input Mapping

When the simulator is in backend mode - connected via UDP with MAVlink message formatting - it expects the **HIL_ACTUATOR_CONTROLS** message, taking the first four floats:

- `controls[0]` → Motor 0
- `controls[1]` → Motor 1
- `controls[2]` → Motor 2
- `controls[3]` → Motor 3
- `controls[4]..controls[5]` → Motor 4,5 (Hexacopter, Octocopter)
- `controls[6]..controls[7]` → Motor 7 (Octocopter only)

Each value must be in the normalized range:
`0.0 = no thrust`
`1.0 = maximum thrust`

If values are out of range or missing, rotor thrust may be clamped or the drone may fail to lift.

Similarly, when ROS2 plugin interface is active, similar inputs are expected from the actuator topic. See `docs/ros2_interface.md` for more info. 

**Note: Ensure that your flight controller is in sync with the Mixer mapping by checking or edit in the GUI Editor (`Mixer` Tab)**

---

## 5. Summary

- **Frames:** Quadcopter X, Hexacopter, Octocopter
- **Input:** HIL_ACTUATOR_CONTROLS (up to 8 first floats), only needed in MAVLink backend mode  
- **Motor mapping:** Configurable in GUI. Quadcopter defaults to 0: FR (CCW), 1: RL (CCW), 2: FL (CW), 3: RR (CW)  

---



