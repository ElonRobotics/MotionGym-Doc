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
