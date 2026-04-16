---
layout: default
title: Robot Model Endpoint
parent: HTTP API
nav_order: 4
lang: en
ref: http-api-robot
permalink: /en/docs/reference/http-api/robot-model.html
---

# Robot Model

## GET /robot-model

Parse the current robot model (URDF or MJCF) and return DOF, joint limits, and topology.

### Response

```json
{
  "robotName": "g1",
  "rootLink": "pelvis",
  "dofCount": 29,
  "dofNames": ["left_hip_roll", ...],
  "joints": [
    {
      "name": "left_hip_roll",
      "type": "revolute",
      "parentLink": "left_hip",
      "childLink": "left_thigh",
      "axis": [1, 0, 0],
      "lowerLimit": -3.14,
      "upperLimit": 3.14,
      "velocityLimit": 6.28
    }
  ]
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `robotName` | `string` | Robot name |
| `rootLink` | `string` | Root link name |
| `dofCount` | `usize` | DOF count |
| `dofNames` | `string[]` | DOF name list |
| `joints` | `JointDetail[]` | Joint detail list |

### JointDetail

| Field | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | Joint name |
| `type` | `string` | Joint type |
| `parentLink` | `string` | Parent link |
| `childLink` | `string` | Child link |
| `axis` | `number[]` | Rotation axis direction |
| `lowerLimit` | `number` | Lower position limit |
| `upperLimit` | `number` | Upper position limit |
| `velocityLimit` | `number` | Velocity limit |

---

## GET /robot-info

Return current robot configuration information.

### Response

```json
{
  "targetRobot": "unitree_g1",
  "modelPath": "/path/to/robot.xml",
  "rootQuatOrder": "xyzw"
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `targetRobot` | `string` | Target robot name |
| `modelPath` | `string` | Robot model file path (URDF or MJCF) |
| `rootQuatOrder` | `string` | Root quaternion order |
