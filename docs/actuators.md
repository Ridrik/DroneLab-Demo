# Actuator Model

Actuators are a core feature in enabling the dynamics and controlability of a vehicle. This software simulates the actuator motors following base motor dynamics and ESC commands. This is used, in turn, both for the dynamics integration, and optionally for firmware-like internal control. 
Two main submodels are used, which affects both the ESC commanding and the physical quantities simulated. This document briefly summarizes both models below, as well as the common motor dynamics.

---

### 1.1. Open-Loop ESC Model

This is the simpler model.

- The throttle command **u ∈ [0, 1]** is applied directly as a **voltage factor**.  
- Motor torque and thrust respond based on the motor’s inherent characteristics.  
- Because this is essentially “voltage → rpm → thrust”, the achieved thrust may differ from the ideal mapping.

#### Implication
- Controllers will likely require **integral action** (e.g., position outer loop) to compensate for steady-state tracking error.
- Good for simple HITL interfaces or high-performance autonav controllers that already include strong feedback.

---

### 1.2. Closed-Loop ESC Model (Advanced)

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

## 2. Actuator Dynamics

Motor rotational dynamics follow:
`J * ω_dot = τ_motor - τ_load`

Where:

- **`τ_motor = kt * I`** (torque constant × motor current)  
- **`τ_load = torqueCoeff * ω^2`** (propeller aerodynamic load)  
- **`I = (V_applied - ke * ω_actual) / R`** (motor current)  

This captures inertia, propeller load, and electrical dynamics.

---

## 3. Thermal and ESC Voltage Modeling (Advanced Mode)

### 3.1 Motor Temperature

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

### 3.2 ESC Voltage

The voltage delivered to the motor:

`V_esc = V_battery - (I * R_wiring) - V_esc_drop + noise`

- Includes wiring resistance and internal ESC voltage drop.  
- Noise can be added to simulate sensor uncertainty.  

### 3.3 ESC Shutdown

The ESC shuts down if:

`V_esc < V_cutoff OR T_motor > T_max`

Which, in turn, nullifies the eletric current, and, by propagation, the motor torque, leading to a rapid deceleration.

This simulates **low-voltage cutoff** and **thermal protection**, preventing overcurrent or overheating.


---

## 4. Summary

- **ESC models:**  
  - Open-loop: throttle → voltage → thrust  
  - Closed-loop: throttle → speed → PID → voltage → thrust  
- **Actuator dynamics:** rotational + electrical  
- **Advanced mode:** includes motor temperature, ESC voltage drop, ESC shutdown logic

---
