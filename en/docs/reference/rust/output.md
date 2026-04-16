---
layout: default
title: Export
parent: Rust API
nav_order: 6
lang: en
ref: rust-api-output
permalink: /en/docs/reference/rust/output.html
---

# Export

## RetargetOutput

| Field | Type | Description |
| :--- | :--- | :--- |
| `fps` | `u32` | Output frame rate |
| `root_pos_frames` | `Vec<[f64; 3]>` | Root position sequence |
| `root_rot_frames` | `Vec<[f64; 4]>` | Root rotation sequence (xyzw) |
| `dof_pos_frames` | `Vec<Vec<f64>>` | Joint DOF sequence |

### Methods

| Method | Description |
| :--- | :--- |
| `to_csv()` | Export as CSV string |
| `to_json(options)` | Export as JSON string |
| `to_export_sequence(options)` | Build the unified export structure `ExportSequence` |
| `export_to_file(path, options)` | Export to file, supporting `csv` / `json` / `npz` / `pkl` |

`to_csv()` produces a CSV with three sections: root translation, root rotation, and joint DOFs.

---

## Unified Export Layer

`RetargetOutput` now exports through a unified export layer. The core configuration is controlled by `ExportFormat` and `ExportOptions`.

### ExportFormat

```rust
pub enum ExportFormat {
    Csv,
    Json,
    Npz,
    Pkl,
}
```

| Format | Description |
|--------|-------------|
| `Csv` | Default format, compatible with the original retarget output |
| `Json` | Structured export for debugging and service integration |
| `Npz` | NumPy compressed format for Python / ML pipelines |
| `Pkl` | Python Pickle format for downstream Python tools |

### ExportOptions

```rust
pub struct ExportOptions {
    pub format: ExportFormat,
    pub include_velocities: bool,
    pub include_meta: bool,
    pub pretty_json: bool,
}
```

| Field | Type | Description |
|-------|------|-------------|
| `format` | `ExportFormat` | Export format |
| `include_velocities` | `bool` | Whether to include linear / angular / joint velocities |
| `include_meta` | `bool` | Whether to include metadata in the export |
| `pretty_json` | `bool` | Whether JSON output should be pretty-printed |

Default values:

```rust
ExportOptions::default()
```

- `format = Csv`
- `include_velocities = false`
- `include_meta = true`
- `pretty_json = true`

### ExportSequence

Unified export structure:

```rust
pub struct ExportSequence {
    pub meta: Option<ExportMeta>,
    pub signals: ExportSignals,
}
```

#### ExportMeta

| Field | Type | Description |
|-------|------|-------------|
| `fps` | `u32` | Frame rate |
| `frame_count` | `usize` | Frame count |
| `robot` | `Option<String>` | Robot name if available |
| `source` | `Option<String>` | Source identifier if available |
| `dof_names` | `Option<Vec<String>>` | DOF names if available |
| `rotation_format` | `String` | Quaternion order, currently `xyzw` |
| `position_unit` | `String` | Position unit, currently `meter` |
| `schema_version` | `u32` | Export schema version |

#### ExportSignals

| Field | Type | Description |
|-------|------|-------------|
| `root_pos` | `Vec<[f64; 3]>` | Root position sequence |
| `root_rot_xyzw` | `Vec<[f64; 4]>` | Root rotation sequence |
| `dof_pos` | `Vec<Vec<f64>>` | Joint DOF sequence |
| `root_lin_vel` | `Option<Vec<[f64; 3]>>` | Root linear velocity |
| `root_ang_vel` | `Option<Vec<[f64; 3]>>` | Root angular velocity |
| `dof_vel` | `Option<Vec<Vec<f64>>>` | Joint velocity |

---

## Export Examples

### Export CSV

```rust
use ee_sdk::prelude::*;

let output = retarget_from_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    BvhRetargetOptions::new(Robot::UnitreeG1),
)?;

std::fs::write("output.csv", output.to_csv())?;
```

### Export JSON

```rust
use ee_sdk::{ExportFormat, ExportOptions};

let json = output.to_json(&ExportOptions {
    format: ExportFormat::Json,
    include_velocities: true,
    ..Default::default()
})?;

std::fs::write("output.json", json)?;
```

### Export NPZ / PKL

`npz` and `pkl` are exported through `export_to_file()`, which uses the Python export bridge underneath.

```rust
use ee_sdk::{ExportFormat, ExportOptions};

output.export_to_file(
    "output.npz",
    &ExportOptions {
        format: ExportFormat::Npz,
        include_velocities: true,
        ..Default::default()
    },
)?;

output.export_to_file(
    "output.pkl",
    &ExportOptions {
        format: ExportFormat::Pkl,
        include_velocities: true,
        ..Default::default()
    },
)?;
```

{: .note }
> `npz` and `pkl` export depend on the Python bridge script `scripts/export_retarget.py`, so the runtime environment must have `python3` available.

---

## FrameOutput

Return type of `RetargetSession::retarget_frame()`.

| Field | Type | Description |
| :--- | :--- | :--- |
| `root_pos` | `[f64; 3]` | Root position for single frame |
| `root_rot_xyzw` | `[f64; 4]` | Root rotation quaternion (xyzw) |
| `dof_pos` | `Vec<f64>` | DOF values for single frame |
