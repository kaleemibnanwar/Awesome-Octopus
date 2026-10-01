# Feature Specification: Edge Hardware Actuation & Spatial Telemetry 🦾📡

> **Real-Time Kinematics, Sensor Fusion & Physical-Digital Hardware Control**  
> Integrated across: `octolimb`, `OctopusMCP-Manager`, `OctopusStudio`

---

## 📌 Overview

The **Edge Hardware Actuation & Spatial Telemetry** feature provides the physical bridge between digital AI intelligence and physical robotic hardware. It translates high-level spatial instructions into real-time joint-space motor trajectories, safety velocity envelopes, and continuous sensor telemetry feeds.

---

## 🏛 Hardware Control Loop Architecture

```
┌────────────────────────────────────────────────────────┐
│             Autonomous Agent / Studio Goal             │
│   ("Pick up vial from Tray A and place in Centrifuge") │
└───────────────────────────┬────────────────────────────┘
                            │ Cartesian Goal: (X, Y, Z, Roll, Pitch, Yaw)
                            ▼
┌────────────────────────────────────────────────────────┐
│             octolimb Spatial Kinematics Core           │
│  - Inverse Kinematics (IK) Multi-solution Solver       │
│  - Collision Geofence & Velocity Profile Generator     │
│  - Trajectory Smoothing (Quintic Spline Interpolation) │
└───────────────────────────┬────────────────────────────┘
                            │ 1000Hz Joint Angle Velocity Setpoints (θ1..θ6)
                            ▼
┌────────────────────────────────────────────────────────┐
│              Hardware Abstraction Layer (HAL)          │
│  (CANOpen / EtherCAT / Micro-ROS / Serial Protocols)   │
└───────────────────────────┬────────────────────────────┘
                            │ Current / Voltage / Torque Commands
                            ▼
┌────────────────────────────────────────────────────────┐
│        Physical Actuators, Grippers & Sensor Array     │
│  - 6-DoF Robotic Arm Joints                            │
│  - Optical Encoders, Force/Torque Strain Gauges        │
│  - Laser Distance Sensors & Infrared Limits            │
└────────────────────────────────────────────────────────┘
```

---

## ⚙️ Core Technical Capabilities

### 1. Dual-Space Kinematics Engine
- **Forward Kinematics (FK)**: Calculates end-effector Cartesian poses from joint angles in `< 15 microseconds`.
- **Inverse Kinematics (IK)**: Resolves multi-joint configurations with singularity avoidance and user-configurable joint limit constraints.

### 2. High-Frequency Real-Time Telemetry Stream
- Emits 100Hz telemetry packets containing:
  - 6-axis Cartesian position and velocity vectors.
  - Joint motor currents, temperatures, and torque loads.
  - End-effector force-torque sensor readouts.
- Visualized live within `OctopusStudio` via a real-time WebGL 3D digital twin.

### 3. Safety Envelope & Torque-Limiting Interlocks
- **Iso-compliant Safety Envelopes**: Soft virtual walls preventing actuators from colliding with equipment or human workspaces.
- **Dynamic Current Monitoring**: Triggers instant microsecond motor disengagement if resistance exceeds programmed thresholds (e.g. delicate glassware handling).

### 4. Direct MCP Hardware Interface
- Provides intuitive, high-level MCP tool invocations:
  - `octolimb_move_cartesian(x, y, z, r, p, y, speed)`
  - `octolimb_actuate_gripper(grip_force, target_mm)`
  - `octolimb_set_virtual_geofence(box_min, box_max)`
  - `octolimb_emergency_stop(reason)`

---

## 📄 Real-Time Telemetry Data Frame

```json
{
  "timestamp_us": 1716382910452300,
  "arm_id": "octolimb-manipulator-04",
  "operational_state": "TRAJECTORY_FOLLOWING",
  "pose": {
    "cartesian_mm": { "x": 240.5, "y": -110.2, "z": 85.0 },
    "orientation_deg": { "roll": 0.0, "pitch": -88.5, "yaw": 12.4 }
  },
  "joints": [
    { "id": 1, "angle_deg": 12.4, "torque_nm": 1.8, "temp_c": 36.2 },
    { "id": 2, "angle_deg": -45.1, "torque_nm": 4.2, "temp_c": 38.5 },
    { "id": 3, "angle_deg": 68.0, "torque_nm": 3.1, "temp_c": 37.0 },
    { "id": 4, "angle_deg": 0.0, "torque_nm": 0.5, "temp_c": 32.1 },
    { "id": 5, "angle_deg": -23.5, "torque_nm": 0.8, "temp_c": 31.4 },
    { "id": 6, "angle_deg": 0.0, "torque_nm": 0.2, "temp_c": 30.2 }
  ],
  "end_effector": {
    "gripper_aperture_mm": 45.0,
    "contact_force_n": 2.1,
    "has_payload": true
  }
}
```

---

## 📊 Performance & Kinematics Benchmarks

| Metric | Specification |
| :--- | :--- |
| **IK Calculation Time** | `< 25 µs` on embedded ARM64 |
| **Control Loop Frequency** | `1,000 Hz` (EtherCAT) / `200 Hz` (CAN) |
| **Position Repeatability** | `± 0.05 mm` |
| **E-STOP Response Time** | `< 4 ms` hardware cut-off |
| **Telemetry Latency** | `< 8 ms` end-to-end to OctopusStudio |
