# Quadcopter Actuator Model (X Configuration)

This document describes the quadcopter actuator system used in the simulator.  
It covers the motor geometry, MAVLink actuator input mapping, and the two internal
ESC/thrust models (open-loop and closed-loop).

---

## 1. Frame Geometry: Quadcopter X

The quadcopter is modelled in **body NED coordinates**:

- **+X** → forward  
- **+Y** → right  
- **+Z** → down  

The quad is in **X configuration**, with motors arranged diagonally around the center of mass.

### Motor Indexing and Rotation Directions

Motor indices follow the simulator’s **actuator order 0–3**, all normalized 0–1.

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

## 2. Backend Actuator Input Mapping

When the simulator is in backend mode - connected via UDP with MAVlink message formatting - it expects the **HIL_ACTUATOR_CONTROLS** message, taking the first four floats:

- `controls[0]` → Motor 0 (Front Right)
- `controls[1]` → Motor 1 (Rear Left)
- `controls[2]` → Motor 2 (Front Left)
- `controls[3]` → Motor 3 (Rear Right)

Each value must be in the normalized range:
`0.0 = no thrust`
`1.0 = maximum thrust`

If values are out of range or missing, rotor thrust may be clamped or the drone may fail to lift.

---

## 3. Actuator / ESC Models

The simulator currently supports **two actuator modelling modes**:

---

### 3.1. Open-Loop ESC Model

This is the simpler model.

- The throttle command **u ∈ [0, 1]** is applied directly as a **voltage factor**.  
- Motor torque and thrust respond based on the motor’s inherent characteristics.  
- Because this is essentially “voltage → rpm → thrust”, the achieved thrust may differ from the ideal mapping.

#### Implication
- Controllers will likely require **integral action** (e.g., position outer loop) to compensate for steady-state tracking error.
- Good for simple HITL interfaces or high-performance autonav controllers that already include strong feedback.

---

### 3.2. Closed-Loop ESC Model (Advanced)

This model introduces **sensor feedback inside the ESC**.

1. Motor angular speed is measured (`ω_actual` in rad/s).  
2. Throttle command is mapped to **desired speed**:  
`ω_desired = throttle_to_speed(u)`
3. The ESC computes a tracking error:  
`e = ω_desired - ω_actual`
4. A **PID controller** acts on this error to produce a corrected voltage factor:  
`V_command = PID(e)`

#### Benefits
- Much tighter control of RPM → thrust.  
- Reduces dependence on outer-loop integrators.  
- More realistic if your target system models advanced/proprietary ESCs or FOC-based controllers.

---

## 4. Actuator Dynamics

Motor rotational dynamics follow:
`J * ω_dot = τ_motor - τ_load`

Where:

- **`τ_motor = kt * I`** (torque constant × motor current)  
- **`τ_load = torqueCoeff * ω^2`** (propeller aerodynamic load)  
- **`I = (V_applied - ke * ω_actual) / R`** (motor current)  

This captures inertia, propeller load, and electrical dynamics.

---

## 5. Thermal and ESC Voltage Modeling (Advanced Mode)

### 5.1 Motor Temperature

Motor temperature `T` is updated as a first-order thermal system:
**τ_thermal * dT/dt = T_target - T_current**
Where the **target temperature** is based on power dissipation:
```
P_elec = I^2 * R
P_mech = k_mech * ω
T_target = T_ambient + (P_elec + P_mech) * R_th
```


- Sensor noise can be added for realism.  
- Temperature is clamped between ambient and maximum ESC temperature.

### 5.2 ESC Voltage

The voltage delivered to the motor:

`V_esc = V_battery - (I * R_wiring) - V_esc_drop + noise`

- Includes wiring resistance and internal ESC voltage drop.  
- Noise can be added to simulate sensor uncertainty.  

### 5.3 ESC Shutdown

The ESC shuts down if:

`V_esc < V_cutoff OR T_motor > T_max`

This simulates **low-voltage cutoff** and **thermal protection**, preventing overcurrent or overheating.


---

## 6. Summary

- **Frame:** Quadcopter X, body NED frame  
- **Input:** HIL_ACTUATOR_CONTROLS (first four floats), only needed in MAVLink backend mode  
- **Motor mapping:** 0: FR (CCW), 1: RL (CCW), 2: FL (CW), 3: RR (CW)  
- **ESC models:**  
  - Open-loop: throttle → voltage → thrust  
  - Closed-loop: throttle → speed → PID → voltage → thrust  
- **Actuator dynamics:** rotational + electrical  
- **Advanced mode:** includes motor temperature, ESC voltage drop, ESC shutdown logic

---



