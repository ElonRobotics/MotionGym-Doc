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

`ee-api` provides the HTTP surface used by WebUI and service integrations.

## Endpoint Overview

| Endpoint | Description |
|----------|-------------|
| `POST /skeleton/bvh` | Extract skeleton data from BVH |
| `POST /convert/bvh` | Normalize and export BVH |
| `POST /export/standard-bvh` | Export standard skeleton BVH |
| `POST /retarget/bvh` | Retarget BVH to robot pose |
| `POST /robot-model` | Query robot model information |
| `POST /robot-info` | Query robot basic information |
| `POST /batch/retarget/bvh` | Batch BVH retargeting |
| `POST /batch/convert/bvh` | Batch BVH normalization |
| `POST /batch/export/standard-bvh` | Batch export standard skeleton BVH |
