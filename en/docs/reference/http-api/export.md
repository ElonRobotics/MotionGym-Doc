---
layout: default
title: Export Endpoint
parent: HTTP API
nav_order: 6
lang: en
ref: http-api-export
permalink: /en/docs/reference/http-api/export.html
---

# Export

## POST /post-process/csv

Performs unified export processing on a retarget CSV file, supporting:

- keeping CSV output
- exporting to JSON
- exporting to NPZ
- exporting to PKL
- optional resampling
- optional velocity signals

The input to this endpoint is a **retarget CSV exported from RetargetOutput**, not a raw BVH file.

### Request

```json
{
  "inputPath": "/path/to/output.csv",
  "outputPath": "/path/to/output.json",
  "format": "json",
  "fps": 30,
  "resampleFps": 60,
  "includeVelocities": true
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `inputPath` | `string` | Yes | Input CSV file path |
| `outputPath` | `string` | Yes | Output file path |
| `format` | `string` | No | Output format: `csv` / `json` / `npz` / `pkl`, default `csv` |
| `fps` | `number` | Yes | Frame rate of the input CSV. Required because CSV does not store FPS |
| `resampleFps` | `number` | No | If set, resample to the target FPS before exporting |
| `includeVelocities` | `boolean` | No | Whether to append root linear velocity, root angular velocity, and joint velocities |

### Response

```json
{
  "fps": 60,
  "frameCount": 200,
  "format": "json",
  "outputPath": "/path/to/output.json"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `fps` | `u32` | Output frame rate |
| `frameCount` | `usize` | Output frame count |
| `format` | `string` | Actual output format |
| `outputPath` | `string` | Actual output file path |

### Supported format Values

| Value | Description |
|-------|-------------|
| `csv` | Keep CSV output |
| `json` | Export unified JSON structure |
| `npz` | Export NumPy compressed file |
| `pkl` | Export Python Pickle file |

### JSON Output Structure

When `format = "json"`, the output file follows the same unified export structure as the Rust SDK:

```json
{
  "meta": {
    "fps": 30,
    "frameCount": 100,
    "rotationFormat": "xyzw",
    "positionUnit": "meter",
    "schemaVersion": 1
  },
  "signals": {
    "rootPos": [[0, 0, 0.8]],
    "rootRotXyzw": [[0, 0, 0, 1]],
    "dofPos": [[0.1, 0.2]]
  }
}
```

If `includeVelocities = true`, the file also includes:

- `rootLinVel`
- `rootAngVel`
- `dofVel`

{: .note }
> `npz` and `pkl` export depend on the Python export bridge, so the runtime environment must have `python3` available.
