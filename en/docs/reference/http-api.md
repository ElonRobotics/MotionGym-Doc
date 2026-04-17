---
layout: default
title: HTTP API
nav_order: 5
has_toc: true
description: ee-api HTTP endpoint reference
lang: en
ref: http-api
permalink: /en/docs/reference/http-api.html
---

# HTTP API

`ee-api` provides the local HTTP surface used by WebUI and service integrations.

The stable public surface is intentionally narrow and targets fixed-config `unitree_g1` BVH retargeting. The remaining endpoints are kept as compatibility/internal routes.

## Endpoint Overview

| Endpoint | Description | Status |
|----------|-------------|--------|
| `GET /health` | Health check | Stable public |
| `GET /supported/sources` | Query supported source list | Stable public |
| `GET /supported/robots` | Query supported robot list | Stable public |
| `GET /robot-info` | Query robot model path and root quaternion order | Stable public |
| `POST /retarget/bvh` | Retarget BVH to robot pose | Stable public |
| `GET /supported` | Query default source / target | Compatibility-only |
| `POST /skeleton/bvh` | Extract skeleton data from BVH | Compatibility-only |
| `POST /convert/bvh` | Normalize and export BVH | Compatibility-only |
| `POST /export/standard-bvh` | Export standard skeleton BVH | Compatibility-only |
| `POST /batch/retarget/bvh` | Batch BVH retargeting | Compatibility-only |
| `POST /batch/convert/bvh` | Batch BVH normalization | Compatibility-only |
| `POST /batch/export/standard-bvh` | Batch export standard skeleton BVH | Compatibility-only |
| `POST /post-process/csv` | Unified export processing for retarget CSV | Compatibility-only |

## Error Responses

Business errors currently use `400 Bad Request` and return at least:

```json
{
  "error": "..."
}
```

For BVH-related endpoints, the service validates the BVH after actually loading it, then returns structured error fields while keeping the original `error` field compatible:

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

Current BVH error codes are intentionally reduced to 3:

- `BVH_SYNTAX_ERROR`
- `BVH_FORMAT_ERROR`
- `BVH_PARSE_FAILED`
