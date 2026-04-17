---
layout: default
title: Data Import
parent: HTTP API
nav_order: 2
lang: en
ref: http-api-input
permalink: /en/docs/reference/http-api/input-skeleton.html
---

# Data Import

## POST /skeleton/bvh

Extract skeleton data from a BVH file.

This endpoint returns a BVH skeleton snapshot for inspecting the source motion skeleton and per-frame poses. It does not contain robot DOF information, so there is no `dofJoints` or similar field in the response.

### Request

```json
{
  "bvhPath": "/path/to/motion.bvh",
  "axisOrder": "XYZ"
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `bvhPath` | `string` | BVH file path |
| `axisOrder` | `string` | Rotation axis order override |

### Response

```json
{
  "fps": 60,
  "frameCount": 100,
  "joints": ["Hips", "Spine", "LeftUpLeg", ...],
  "positions": [x,y,z, x,y,z, ...],
  "rotations": [x,y,z,w, x,y,z,w, ...]
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `fps` | `u32` | Frame rate |
| `frameCount` | `usize` | Frame count |
| `joints` | `string[]` | BVH skeleton joint name list |
| `positions` | `number[]` | `frameCount × joints × 3`, position data |
| `rotations` | `number[]` | `frameCount × joints × 4`, order is `xyzw` |

### Error Response

`POST /skeleton/bvh` uses the same BVH validation flow as `/retarget/bvh`: the file is loaded first, then checked against the currently supported format constraints.

Example:

```json
{
  "error": "BVH syntax error: unsupported joint naming such as 'mixamorig:' was detected",
  "code": "BVH_SYNTAX_ERROR",
  "category": "bvh_syntax_error",
  "details": {
    "path": "/path/to/input.bvh",
    "detectedToken": "mixamorig:"
  },
  "retryable": false
}
```

Only 3 BVH error codes are currently used:

- `BVH_SYNTAX_ERROR`
- `BVH_FORMAT_ERROR`
- `BVH_PARSE_FAILED`

---

## POST /convert/bvh

Normalize and export a BVH file.

### Request

```json
{
  "inputPath": "/path/to/source.bvh",
  "outputPath": "/path/to/output.bvh",
  "axisOrder": "ZXY",
  "fps": 60
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `inputPath` | `string` | Input BVH path |
| `outputPath` | `string` | Output BVH path |
| `axisOrder` | `string` | Rotation axis order override |
| `fps` | `number` | Target frame rate |

### Response

```json
{
  "fps": 60,
  "frameCount": 100
}
```

### Error Response

`POST /convert/bvh` performs the same structural BVH validation before writing output.

The response format remains compatible with the original `error` field and may also include:

- `code`
- `category`
- `details`
- `retryable`

---

## POST /export/standard-bvh

Export standard skeleton BVH.

### Request

```json
{
  "inputPath": "/path/to/retarget.bvh",
  "outputPath": "/path/to/standard.bvh"
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `inputPath` | `string` | Input path |
| `outputPath` | `string` | Output path |

### Response

```json
{
  "fps": 60,
  "frameCount": 100
}
```

### Error Response

`POST /export/standard-bvh` also loads and validates the input BVH before exporting.

Unsupported BVH input returns the same structured error object shape as `/retarget/bvh`.
