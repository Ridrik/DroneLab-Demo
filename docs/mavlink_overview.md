# MAVLink Interface (Early Implementation)

The simulator implements a subset of standard MAVLink messages for Hardware-in-the-Loop (HIL) simulation. All messages follow official MAVLink definitions (see MAVLink documentation).

This document describes the behavior of the current implementation.

---

## 1. Heartbeat and Connection List

The simulator publishes standard `HEARTBEAT` messages at **1 Hz** when a session is running.
Additionally, it checks for incoming hearbeats even before a session is running. Users should, therefore, check active connections, on the `MAVLink` tab, to ensure that your connected systems are recognized, given the setup chosen (ip, ports...)

**Fields:**

- **MAV_TYPE:** `MAV_TYPE_GENERIC`  
- **MAV_AUTOPILOT:** `MAV_AUTOPILOT_INVALID`  
- **system_status:**  
  - `MAV_STATE_STANDBY` when simulation is paused or finished/stopped  
  - `MAV_STATE_ACTIVE` when simulation is running  
- **base_mode:**  
  - When active: `MAV_MODE_FLAG_SAFETY_ARMED | MAV_MODE_FLAG_GUIDED_ENABLED`  
  - Otherwise: appropriate unarmed / standby combinations

The simulator also publishes `HOME_POSITION` with the reference home coordinates on a regular basis.

**Home Position:**  

- The simulator also publishes `HOME_POSITION` at **1 Hz** when a simulation is running.  
- Default reference coordinates are defined per scenario.  

> These messages allow flight controllers to detect that the simulated vehicle is alive and its state.

---

## 2. Mission Handling (Simulator as Mission Source)

In the current version, the simulator acts as the **source of truth for mission data**.  
(Plug-in or external mission sources may be added in future updates.)

**Mission upload flow:**

1. When the user starts the simulation, the simulator immediately sends `MISSION_COUNT` (`MAVLINK_MSG_ID_MISSION_COUNT_LEN` id).  
2. External flight controllers should request mission items using `MISSION_REQUEST_INT` id.  
3. The simulator responds with the requested waypoint - `MISSION_ITEM_INT` id.  
4. Once all items are sent, the flight controller is expected to reply with mission acknowledgment (`MAVLINK_MSG_ID_MISSION_ACK` id).

**Note:**  
External mission uploads are not yet supported; the simulator always provides the mission defined in the active scenario.

---

## 3. HIL Sensor Data Output

When a simulation session is active, the simulator publishes `HIL_SENSOR` (`MAVLINK_MSG_ID_HIL_SENSOR` id) and optionally `HIL_GPS` (`MAVLINK_MSG_ID_HIL_GPS` id).

### HIL_SENSOR

HIL_SENSOR are published at the simulator IMU frequency rate. The following signals are, therefore, **always included**:

- Gyroscope  
- Accelerometer  

The following are **optional**, depending on sensor availability and update rates (Controlled on the GUI):

- Magnetometer  
- Barometer  

The simulator updates only the fields corresponding to active sensors. The external flight controller must check the `fields_updated` field to know which sensor data is updated.

### HIL_GPS

`HIL_GPS` is sent only if the GPS sensor is enabled for the session.

---

## 4. Actuator Input (HIL_ACTUATOR_CONTROLS)

The simulator subscribes to `HIL_ACTUATOR_CONTROLS` as its primary actuator interface.

**Actuator rules:**

- Only the **first four float channels** are used  
- Values are **normalized to 0.0–1.0**  
- Default vehicle: **Quadcopter X** configuration  
  - Channel mapping: **Configurable in the GUI Editor, under the `Mixer` Tab**
- Incorrect or missing values prevent vehicle operation

> More vehicle types will be supported in future versions.

---

## 5. Simulation States

| Simulator state | Heartbeat `system_status` | Sensor publication | Notes |
|-----------------|---------------------------|------------------|-------|
| Running         | `MAV_STATE_ACTIVE`        | All active sensors | Normal operation |
| Paused          | `MAV_STATE_STANDBY`       | None | actuator commands ignored |
| Stopped         | `MAV_STATE_POWEROFF`      | None | Run ended; actuator commands ignored |

---

## 6. Message Flow Overview

```
       Simulator
      ┌───────────────┐
      │ Heartbeat     │───► Flight Controller
      │ Home Position │───► Flight Controller
      │ Mission Items │───► Flight Controller
      │ HIL_SENSOR    │───► Flight Controller
      │ HIL_GPS       │───► Flight Controller
      │ HIL_ACTUATORS │◄── Flight Controller
      └───────────────┘
```

- Sensors and home/mission info are **published from the simulator**  
- Sensor data contain the simulation time in microseconds. Note that this may or may not follow real time, due to options to pause, slow or fasten simulation during a live run.
- Actuator commands are **received from the flight controller**  
- Mission flow is **simulator-provided only** in this version

---

## 7. Summary

**Publishes:**  

- `HEARTBEAT` (1 Hz)  
- `HOME_POSITION` (1 Hz)  
- Mission broadcast (`MISSION_COUNT`, `MISSION_ITEM*`)  
- `HIL_SENSOR` (IMU update rate)  
- `HIL_GPS` (if enabled)

**Receives:**  

- `HIL_ACTUATOR_CONTROLS` (channels 0–3, normalized 0–1, Quad X mapping on GUI)

**Mission flow:** Simulator-provided only

**Default assumptions:** Quadcopter X; more vehicle types will be added
