---
layout: default
title: Shared Field Conventions
parent: HTTP API
nav_order: 1
lang: en
ref: http-api-conventions
permalink: /en/docs/reference/http-api/conventions.html
---

# Core Concepts

## axisOrder

Supported values: `XYZ`, `XZY`, `YXZ`, `YZX`, `ZXY`, `ZYX`.

Behavior when provided:

- Takes effect after hierarchy parsing
- Overrides rotation channel order before motion conversion
- Position channel remains unchanged

## Quaternion Order

- `/skeleton/bvh` uses `xyzw` for `rotations`
- `/retarget/bvh` uses `xyzw` for `rootRotations`

## Flattened Array Format

| Field | Format |
| :--- | :--- |
| `positions` | `frameCount × joints × 3` |
| `rotations` | `frameCount × joints × 4` |
| `rootPositions` | `frameCount × 3` |
| `rootRotations` | `frameCount × 4` |
| `dofPositions` | `frameCount × dofCount` |

## Common Error Response

```json
{
  "error": "error message string"
}
```
