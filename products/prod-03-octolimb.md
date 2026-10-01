# Product Specification: octolimb 🦾⚡

> **Robotic Hardware Actuation, Edge Agent Runtime & Spatial Telemetry Controller**  
> Repository: [github.com/kaleemibnanwar/octolimb](https://github.com/kaleemibnanwar/octolimb)

---

## 📌 Executive Summary

**octolimb** is an open-source robotic actuation framework, hardware abstraction layer (HAL), and edge agent runtime designed for physical-digital interaction. It enables autonomous software agents and multi-modal models to control multi-axis robotic arms, mobile rovers, pan-tilt camera gimbals, tactile end-effectors, and IoT actuators over deterministic real-time communication buses.

By encapsulating forward/inverse kinematics, safety velocity envelopes, spatial coordinate frames, and sensory feedback loops into clean, standardized primitives and MCP tool interfaces, `octolimb` provides the physical "limbs" for digital AI brains.

---

## 🏛 Architecture Overview

```
                      ┌─────────────────────────────────────────┐
                      │    Autonomous Agent / OctopusStudio     │
                      │       (Natural Language / LLM Plan)     │
                      └────────────────────┬────────────────────┘
                                           │ (MCP / JSON-RPC / gRPC)
                      ┌────────────────────┴────────────────────┐
                      │            octolimb Edge Daemon         │
                      └────────────────────┬────────────────────┘
                                           │
         ┌─────────────────────────────────┼─────────────────────────────────┐
         ▼                                 ▼                                 ▼
┌──────────────────┐             ┌───────────────────┐             ┌──────────────────┐
│ Kinematic Solver │             │ Safety Boundary & │             │ Spatial Feedback │
│ (IK / FK / Traj) │             │ Collision Checker │             │ Sensor Fusion    │
└────────┬─────────┘             └─────────┬─────────┘             └────────┬─────────┘
         │                                 │                                │
         └─────────────────────────────────┼────────────────────────────────┘
                                           ▼
                      ┌─────────────────────────────────────────┐
                      │        Hardware Abstraction Layer       │
                      │  (CAN Bus / ROS2 / Serial / EtherCAT)   │
                      └────────────────────┬────────────────────┘
                                           │
         ┌─────────────────────────────────┼─────────────────────────────────┐
         ▼                                 ▼                                 ▼
┌──────────────────┐             ┌───────────────────┐             ┌──────────────────┐
│ Robotic Arm /    │             │ Pan-Tilt Gimbal / │             │ Tactile Gripper/ │
│ 6-DoF Manipulator│             │ Camera Tracker    │             │ Haptic Sensor    │
└──────────────────┘             └───────────────────┘             └──────────────────┘
```

---

## 🚀 Key Features

### 1. Hardware-Agnostic Abstraction Layer (HAL)
- Unified driver interface for servos, stepper motors, brushless DC (BLDC) actuators, Dynamixel, CANOpen, EtherCAT, and serial microcontrollers (ESP32, STM32, Arduino).
- Native ROS2 (Robot Operating System) bridge and direct micro-ROS support.

### 2. Real-Time Inverse Kinematics & Trajectory Engine
- Analytical and numerical IK solvers supporting 3-DoF to 7-DoF robotic arms.
- Cubic and quintic spline trajectory generation with jerk minimization and strict acceleration limits.

### 3. Safety Envelopes & Collision Avoidance
- Real-time geofencing, workspace boundary limits, current-draw torque limiting, and optical emergency-stop (E-STOP) triggers.
- Automatic velocity dampening when approaching human operators or virtual obstacles.

### 4. Native Model Context Protocol (MCP) Server
- Exposes physical robotics capabilities directly to LLM agents:
  - `octolimb_move_to_pose(x, y, z, roll, pitch, yaw, speed_percent)`
  - `octolimb_actuate_gripper(position_mm, effort_limit_nm)`
  - `octolimb_track_target_coordinates(target_x, target_y, target_z)`
  - `octolimb_get_telemetry_snapshot()`

---

## 🔌 Interconnection Interface

`octolimb` forms the physical execution bridge in the Octopus ecosystem:
- **With OctopusStudio**: Provides a real-time 3D digital twin visualizer (Three.js / WebGL) rendered directly in the Studio preview window.
- **With octocut**: Synchronizes camera gimbal pan-tilt actions with automated video capture and timestamped audio-visual tagging.
- **With OctopusMCP-Manager**: Connects as a secured edge-device MCP node with strict authentication and role-based execution constraints.

```json
{
  "device_id": "octolimb-arm-node-01",
  "capabilities": {
    "degrees_of_freedom": 6,
    "payload_capacity_kg": 2.5,
    "gripper": {
      "type": "parallel_electric",
      "max_width_mm": 85.0
    },
    "sensors": ["force_torque", "encoder_feedback", "temperature_celsius"]
  },
  "endpoints": {
    "websocket_telemetry": "ws://192.168.1.120:9000/telemetry",
    "mcp_rpc": "http://192.168.1.120:9000/mcp"
  }
}
```

---

## 💻 Code Example: Multi-Axis Robotic Command

```typescript
import { OctoLimbClient, CoordinateFrame, Vector3D } from '@octopus/octolimb-client';

const limb = new OctoLimbClient({
  host: '192.168.1.120',
  port: 9000,
  emergencyStopListener: (reason) => {
    console.error(`E-STOP Triggered: ${reason}`);
  }
});

await limb.connect();

// Verify system health and joint temperatures
const status = await limb.getSystemStatus();
if (status.isReady) {
  // Move robotic arm to target sample tray position
  await limb.moveToCartesian({
    position: new Vector3D(180.5, -45.2, 120.0), // mm in base frame
    orientation: { pitch: -90, roll: 0, yaw: 45 }, // degrees
    velocityScale: 0.4, // 40% max speed for safety
    frame: CoordinateFrame.WORLD
  });

  // Close precision gripper with force feedback control
  const gripResult = await limb.setGripper({
    targetWidthMm: 22.0,
    forceLimitNewtons: 4.5,
    timeoutMs: 3000
  });

  console.log(`Grip settled: ${gripResult.objectDetected ? 'Object Held' : 'Empty'}`);
}
```

---

## 📊 Technical Specifications & Benchmarks

| Parameter | Specification |
| :--- | :--- |
| **Control Frequency** | Up to 1000 Hz (1 ms loop) over CAN/EtherCAT; 100 Hz over WebSocket |
| **Supported Arm Topologies** | SCARA, Cartesian, 4-DoF Palletizer, 6-DoF Articulated, 7-DoF Redundant |
| **Latency (Agent to Motor)** | `< 12ms` local network; `< 2ms` on-edge embedded |
| **Target Platforms** | Linux (Ubuntu 22.04 / RT-PREEMPT), Raspberry Pi CM4/5, NVIDIA Jetson Orin, ESP32 |
| **Communication Protocols** | ROS2 Humble/Iron, CANOpen, Modbus TCP, WebSocket, gRPC, MCP |
| **Safety Certification** | ISO 10218-1 compliant soft-stops and force threshold auto-cutoff |
