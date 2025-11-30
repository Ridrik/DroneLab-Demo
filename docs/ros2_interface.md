# ROS2 Interface (Plugin Implementation)

The simulator provides a ROS2 interface as a **loadable plugin** (`.so`) that streams sensor data and receives actuator commands.  
This interface is designed for **Linux** systems; Windows support is not currently available.

The ROS2 interface enables researchers to integrate the simulator with ROS2-native nodes, for tasks such as logging, visualization, AI/ML control, or custom controllers.

---

## 1. Plugin Architecture

- The ROS2 interface is a **separate shared library**.  
- The Plugin is loaded dynamically at runtime. Note that, ros2 dependencies are expected for it to load successfully. 
- Initialization:  
  - The plugin calls `rclcpp::init()` if ROS2 is not already running.  
  - Users can optionally provide ROS2-specific arguments (`argc`/`argv`) for parameter overrides.

---

## 2. Published Topics

The plugin publishes sensor and simulation topics at configurable rates:

| Topic (Default) | Message Type | Description | Default Rate |
|-------|--------------|-------------|--------------|
| `/imu/data` | `sensor_msgs/msg/Imu` | IMU measurements (gyro + accelerometer) | Simulator IMU rate |
| `/mag` | `sensor_msgs/msg/MagneticField` | Magnetometer readings | Matches IMU rate if available |
| `/baro` | `sensor_msgs/msg/FluidPressure` | Barometer pressure | Matches barometer update rate |
| `/gps/fix` | `sensor_msgs/msg/NavSatFix` | GPS position | Matches GPS update rate |
| `/sim/time` | `rosgraph_msgs/msg/Clock` | Simulation time (microseconds) | 1 kHz (default) |

**Notes:**

- Only active sensors are published; topics for disabled sensors will either remain empty or publish no new data.  
- Simulation time is based on the simulator’s internal clock and is **paused when the simulation is paused**.  

---

## 3. Subscribed Topics

The plugin subscribes to actuator commands:

| Topic | Message Type | Description |
|-------|--------------|-------------|
| `/actuator` | `std_msgs/msg/Float32MultiArray` | Actuator commands for the simulated vehicle. First four floats correspond to Quadcopter X. The user can map each motor and direction on the GUI Editor under the `Mixer` Tab |

**Notes:**

- Commands must be normalized in the range 0.0–1.0  
- Additional vehicle types will extend the actuator mapping in future releases

---

## 4. ROS2 Parameters and Configuration

- Topic names, QoS settings, and publishing rates can be **configured via the GUI Session settings on the `ROS2` Tab** or **ROS2 parameter overrides**. The session settings is found in `resources/session.json`
- JSON configuration allows fully custom topic remapping without recompilation.  
- Example configurable parameters:

```json
{
  "ros2_settings": {
      "actuator_qos": 10,
      "enable_interface_default": false,
      "node_name": "sensor_interface",
      "sensor_qos": 100,
      "topic_actuator_cmds": "/actuator",
      "topic_baro": "/baro",
      "topic_clock": "/clock",
      "topic_gps": "/gps/fix",
      "topic_imu": "/imu/data",
       "topic_mag": "/mag"
  },
}
```

- ROS2 CLI or programmatic overrides will **take precedence** over JSON defaults.

---

## 5. Simulation State Behavior

| Simulator state | Publishing behavior | Notes |
|-----------------|------------------|-------|
| Running         | All active topics publish normally | Data reflects current simulation step |
| Paused          | No topics published | Simulation clock does not advance |
| Stopped         | No topics published | Plugin remains loaded; messages frozen |

---

## 6. Message Flow Overview

```
       Simulator Plugin
      ┌───────────────┐
      │ /imu/data     │───► ROS2 nodes
      │ /mag          │───► ROS2 nodes
      │ /baro         │───► ROS2 nodes
      │ /gps/fix      │───► ROS2 nodes
      │ /sim/time     │───► ROS2 nodes
      │ /actuator     │◄── ROS2 nodes (Only on Backend Mode)
      └───────────────┘
```

- Sensors and simulation time are published from the simulator plugin  
- When the simulator is on Backend mode, actuator commands are received from ROS2 nodes. Otherwise, these are ignored.
- Topic remapping and QoS are configurable via GUI, JSON or ROS2 parameters

---

## 7. Summary

- **Publishes:**  
  - `/imu/data` (`sensor_msgs/msg/Imu`)  
  - `/mag` (`sensor_msgs/msg/MagneticField`)  
  - `/baro` (`sensor_msgs/msg/FluidPressure`)  
  - `/gps/fix` (`sensor_msgs/msg/NavSatFix`)  
  - `/sim/time` (`rosgraph_msgs/msg/Clock`)  

- **Subscribes:**  
  - `/actuator` (`std_msgs/msg/Float32MultiArray`, Quad X channels 0–3)  

- **Notes:**  
  - Default vehicle: Quadcopter X 
  - Mixer allocation mapping can be configured on the GUI Editor, under `Mixer` Tab.
  - Simulation time reflects simulator internal clock  
  - Plugin is Linux-only for now  
  - JSON configuration allows topic remapping, QoS, and rates  
  - ROS2 parameter overrides take precedence over JSON defaults
