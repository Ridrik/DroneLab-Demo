# MAVLink Interface (Early Implementation)

The simulator uses a subset of standard MAVLink messages. All messages follow their official MAVLink definitions (refer to the MAVLink documentation for details).  
This document describes the behavior in the initial implementation.

---

## 1. Heartbeat and System Status

The simulator publishes standard `HEARTBEAT` messages at a nominal rate.

**Fields:**

- **MAV_TYPE:** `MAV_TYPE_GENERIC`  
- **MAV_AUTOPILOT:** `MAV_AUTOPILOT_INVALID`  
- **system_status:**  
  - `MAV_STATE_STANDBY` when uninitialized or paused  
  - `MAV_STATE_ACTIVE` when simulation is running  
  - `MAV_STATE_POWEROFF` when simulation is stopped  
- **base_mode:**  
  - When active: `MAV_MODE_FLAG_SAFETY_ARMED | MAV_MODE_FLAG_GUIDED_ENABLED`  
  - Otherwise: appropriate unarmed / standby combinations

The simulator also publishes `HOME_POSITION` with the reference home coordinates on a regular basis.

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

The following are **optional**, depending on sensor availability and update rates:

- Magnetometer  
- Barometer  

The simulator updates only the fields corresponding to active sensors. The external flight controller must check the `fields_updated` field to know which sensor data is updated.

### HIL_GPS

`HIL_GPS` is sent only if the GPS sensor is enabled for the session.

---

## 4. Actuator Input (HIL_ACTUATOR_CONTROLS)

The simulator expects incoming `HIL_ACTUATOR_CONTROLS` messages as its primary actuator interface.  
All received actuator commands are logged for future replay.

**Actuator input rules:**

- Only the **first four** float channels are used.  
- Values must be **normalized in the range** `0.0` to `1.0`.  
- Commands correspond to a **Quadcopter X** configuration.  
- Incorrect or missing values will prevent the vehicle from taking off or operating correctly.

---

## 5. Run termination

Currently, no automatic termination is made. The user shall, therefore, stop the run when appropriate.

---

## 6. Summary

- Simulator **publishes:**  
  - `HEARTBEAT`  
  - `HOME_POSITION`  
  - Mission broadcast (`MISSION_COUNT`, `MISSION_ITEM*`)  
  - `HIL_SENSOR`  
  - `HIL_GPS` (if GPS enabled)

- Simulator **receives:**  
  - `HIL_ACTUATOR_CONTROLS` (channels 0–3, normalized 0–1)

- Mission flow is **simulator-provided only** in this version.
