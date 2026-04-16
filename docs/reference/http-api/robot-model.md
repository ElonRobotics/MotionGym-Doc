---
layout: default
title: 机器人模型接口
parent: HTTP API
nav_order: 4
lang: zh
ref: http-api-robot
---

# 机器人查询

## GET /robot-model

解析当前配置中的机器人模型（URDF 或 MJCF），返回 DOF、关节限位和拓扑结构。

### 响应

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

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `robotName` | `string` | 机器人名称 |
| `rootLink` | `string` | 根 link 名称 |
| `dofCount` | `usize` | 自由度数量 |
| `dofNames` | `string[]` | 自由度名称列表 |
| `joints` | `JointDetail[]` | 关节详情列表 |

### JointDetail

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `name` | `string` | 关节名称 |
| `type` | `string` | 关节类型 |
| `parentLink` | `string` | 父 link |
| `childLink` | `string` | 子 link |
| `axis` | `number[]` | 旋转轴方向 |
| `lowerLimit` | `number` | 位置下限 |
| `upperLimit` | `number` | 位置上限 |
| `velocityLimit` | `number` | 速度上限 |

---

## GET /robot-info

返回当前机器人配置信息。

### 响应

```json
{
  "targetRobot": "unitree_g1",
  "modelPath": "/path/to/robot.xml",
  "rootQuatOrder": "xyzw"
}
```

| 字段 | 类型 | 说明 |
| :--- | :--- | :--- |
| `targetRobot` | `string` | 目标机器人名称 |
| `modelPath` | `string` | 机器人模型文件路径（URDF 或 MJCF） |
| `rootQuatOrder` | `string` | 根旋转四元数顺序 |
