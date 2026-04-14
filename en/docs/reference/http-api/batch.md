---
layout: default
title: Batch Processing
parent: HTTP API
nav_order: 5
lang: en
ref: http-api-batch
permalink: /en/docs/reference/http-api/batch.html
---

# Batch Processing

Batch processing endpoints operate on all BVH files in a folder. A single file failure does not abort the overall operation.

---

## POST /batch/retarget/bvh

Batch-retarget all BVH files in a folder to robot joint value CSV files.

### Request

```json
{
  "inputFolder": "/path/to/bvh/folder",
  "outputFolder": "/path/to/output/folder",
  "fps": 30,
  "offsetToGround": false,
  "axisOrder": "ZXY"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `inputFolder` | `string` | Yes | Directory containing BVH files |
| `outputFolder` | `string` | Yes | Output CSV directory |
| `fps` | `number` | No | Output frame rate, default `30` |
| `offsetToGround` | `boolean` | No | Whether to offset root position to ground, default `false` |
| `axisOrder` | `string` | No | Rotation axis order override, e.g. `"ZXY"` |

### Response

```json
{
  "total": 2,
  "succeeded": 2,
  "failed": 0,
  "results": [
    {
      "fileName": "GVHMR_01.bvh",
      "status": "ok",
      "fps": 30,
      "frameCount": 841,
      "outputPath": "/path/to/output/GVHMR_01.csv"
    },
    {
      "fileName": "GVHMR_02.bvh",
      "status": "ok",
      "fps": 30,
      "frameCount": 413,
      "outputPath": "/path/to/output/GVHMR_02.csv"
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `total` | `number` | Total files processed |
| `succeeded` | `number` | Number succeeded |
| `failed` | `number` | Number failed |
| `results[].fileName` | `string` | Original file name |
| `results[].status` | `string` | `"ok"` or `"error"` |
| `results[].fps` | `number` | Output frame rate (success only) |
| `results[].frameCount` | `number` | Output frame count (success only) |
| `results[].outputPath` | `string` | Output file path (success only) |
| `results[].error` | `string` | Error reason (failure only) |

---

## POST /batch/convert/bvh

Batch-normalize all BVH files in a folder and export.

### Request

```json
{
  "inputFolder": "/path/to/bvh/folder",
  "outputFolder": "/path/to/output/folder",
  "axisOrder": "ZXY",
  "fps": 30
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `inputFolder` | `string` | Yes | Directory containing BVH files |
| `outputFolder` | `string` | Yes | Normalized BVH output directory |
| `axisOrder` | `string` | No | Rotation axis order override, default `"ZXY"` |
| `fps` | `number` | No | Target frame rate; omit to keep original |

### Response

```json
{
  "total": 2,
  "succeeded": 2,
  "failed": 0,
  "results": [
    {
      "fileName": "motion_a.bvh",
      "status": "ok",
      "fps": 30,
      "frameCount": 600,
      "outputPath": "/path/to/output/motion_a.bvh"
    }
  ]
}
```

### Output File Naming

Output files use the same name as the input (`{stem}.bvh`) and are placed in `outputFolder`.

---

## POST /batch/export/standard-bvh

Batch-convert all BVH files in a folder to the standard skeleton BVH format.

### Request

```json
{
  "inputFolder": "/path/to/bvh/folder",
  "outputFolder": "/path/to/output/folder"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `inputFolder` | `string` | Yes | Directory containing BVH files |
| `outputFolder` | `string` | Yes | Standard skeleton BVH output directory |

### Response

```json
{
  "total": 2,
  "succeeded": 2,
  "failed": 0,
  "results": [
    {
      "fileName": "motion_a.bvh",
      "status": "ok",
      "fps": 30,
      "frameCount": 600,
      "outputPath": "/path/to/output/motion_a.bvh"
    }
  ]
}
```

### Difference from /batch/convert/bvh

| | `/batch/convert/bvh` | `/batch/export/standard-bvh` |
|---|---|---|
| Output format | BVH (same as input) | Standard skeleton BVH |
| Skeleton | Preserves original skeleton structure | Normalizes to 24-joint standard skeleton |
| Axis order | Configurable | Fixed Z-X-Y |
| Use case | Format normalization | Unified skeleton format for subsequent retargeting |
