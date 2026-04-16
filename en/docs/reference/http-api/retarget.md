---
layout: default
title: Retarget Endpoint
parent: HTTP API
nav_order: 3
lang: en
ref: http-api-retarget
permalink: /en/docs/reference/http-api/retarget.html
---

# Retargeting

## POST /retarget/bvh

Retarget a BVH file to robot poses.

### Request

```json
{
  "bvhPath": "/path/to/motion.bvh",
  "fps": 60,
  "offsetToGround": false,
  "axisOrder": "XYZ",
  "startFrame": 0,
  "endFrame": 100
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `bvhPath` | `string` | BVH file path |
| `fps` | `number` | Output frame rate |
| `offsetToGround` | `boolean` | Translate root to ground contact |
| `axisOrder` | `string` | Rotation axis order override |
| `startFrame` | `number` | Start frame, 0-based inclusive |
| `endFrame` | `number` | End frame, 0-based inclusive |

### Response

```json
{
  "fps": 60,
  "frameCount": 100,
  "rootPositionStride": 3,
  "rootRotationStride": 4,
  "dofCount": 29,
  "rotationOrder": "xyzw",
  "rootPositions": [x,y,z, x,y,z, ...],
  "rootRotations": [x,y,z,w, x,y,z,w, ...],
  "dofPositions": [pos1, pos2, ..., posN, ...]
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `fps` | `u32` | Output frame rate |
| `frameCount` | `usize` | Output frame count |
| `rootPositionStride` | `usize` | Fixed to `3` |
| `rootRotationStride` | `usize` | Fixed to `4` |
| `dofCount` | `usize` | Degree-of-freedom count |
| `rotationOrder` | `string` | Root quaternion order, currently `xyzw` |
| `rootPositions` | `number[]` | `frameCount × 3` |
| `rootRotations` | `number[]` | `frameCount × 4`, order is `xyzw` |
| `dofPositions` | `number[]` | `frameCount × dofCount` |
