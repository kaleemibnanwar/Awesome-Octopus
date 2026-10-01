# Automated Workflow: Edge Telemetry Ingestion & Robotic Actuation 🦾⚡

> **Declarative Blueprint for Closed-Loop Sensor Telemetry, Spatial Kinematics & Physical Gripper Actuation**  
> Orchestrated via: `OctopusStudio` + `octolimb` + `OctopusMCP-Manager`

---

## 📌 Workflow Summary

This workflow orchestrates real-time edge telemetry monitoring, safety velocity clamping, multi-axis joint trajectory execution, and optical gripper feedback for automated physical manipulation cells.

---

## 🔄 Declarative Pipeline DAG

```
               ┌─────────────────────────────────┐
               │ Trigger: Cartesian Target Goal  │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 1: Safety & Geofence Check │
               │ (OctopusMCP-Manager Interlock)  │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 2: Inverse Kinematics Calc │
               │ (octolimb Kinematic Solver)     │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 3: Quintic Trajectory Exec │
               │ (octolimb CAN / EtherCAT Driver)│
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 4: Force-Torque Grip Settle│
               │ (octolimb Tactile Strain Gauge) │
               └────────────────┬────────────────┘
                                │
                                ▼
               ┌─────────────────────────────────┐
               │ Step 5: Studio 3D Twin Update   │
               │ (OctopusStudio WebGL Viewport)  │
               └─────────────────────────────────┘
```

---

## 📄 Declarative DAG JSON Definition

```json
{
  "$schema": "http://octopus.engine/schemas/workflow-v2.json",
  "name": "edge-telemetry-actuation-loop",
  "version": "1.4.0",
  "nodes": [
    {
      "id": "validate_safety_bounds",
      "tool": "octolimb_check_geofence",
      "inputs": {
        "target_pose": "{{inputs.target_pose}}",
        "speed_scale": 0.5
      }
    },
    {
      "id": "execute_arm_motion",
      "tool": "octolimb_move_trajectory",
      "depends_on": ["validate_safety_bounds"],
      "inputs": {
        "target_cartesian": "{{inputs.target_pose}}",
        "interpolation": "quintic_spline",
        "jerk_limit_rad_s3": 12.0
      }
    },
    {
      "id": "engage_tactile_gripper",
      "tool": "octolimb_actuate_gripper",
      "depends_on": ["execute_arm_motion"],
      "inputs": {
        "aperture_mm": 25.0,
        "max_force_newtons": 3.5,
        "slip_detection": true
      }
    },
    {
      "id": "emit_telemetry_checkpoint",
      "tool": "octopus_studio_broadcast_telemetry",
      "depends_on": ["engage_tactile_gripper"],
      "inputs": {
        "channel": "live_3d_digital_twin",
        "status": "{{engage_tactile_gripper.output}}"
      }
    }
  ]
}
```
