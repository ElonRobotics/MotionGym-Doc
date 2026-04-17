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

### Error Response

`POST /retarget/bvh` still preserves the original `error` field, and now also returns structured fields for BVH-related failures.

Example:

```json
{
  "error": "BVH syntax error: unsupported joint naming such as 'mixamorig:' was detected",
  "code": "BVH_SYNTAX_ERROR",
  "category": "bvh_syntax_error",
  "details": {
    "path": "/path/to/motion.bvh",
    "detectedToken": "mixamorig:"
  },
  "retryable": false
}
```

Notes:

- The BVH file is parsed first, then checked against the currently supported format constraints
- `code` is intended for programmatic handling
- `code` is currently limited to `BVH_SYNTAX_ERROR`, `BVH_FORMAT_ERROR`, and `BVH_PARSE_FAILED`
- `category` is currently limited to `bvh_syntax_error`, `bvh_format_error`, and `bvh_parse_error`
- `details.path` points to the input file that triggered the error
