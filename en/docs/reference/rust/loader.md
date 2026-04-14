---
layout: default
title: Data Import
parent: Rust API
nav_order: 2
lang: en
ref: rust-api-loader
permalink: /en/docs/reference/rust/loader.html
---

# Data Import

## load_bvh

Load and parse a BVH file into normalized frame data.

```rust
pub fn load_bvh(
    path: &str,
    max_frames: i64,
    axis_order: Option<&str>,
) -> Result<BvhLoadResult>
```

### Parameters

| Param | Type | Description |
|-------|------|-------------|
| `path` | `&str` | BVH file path |
| `max_frames` | `i64` | Max frames to load; `-1` = no limit |
| `axis_order` | `Option<&str>` | Rotation axis order override, e.g. `Some("ZXY")`; `None` uses file declaration |

### axis_order Behavior

- Only overrides rotation channel names, does not change values
- `Some("XYZ")`: Force XYZ order to interpret rotation data
- `None`: Use the order declared in file header (recommended)

### Example

```rust
use ee_sdk::ee_core::loader::bvh::load_bvh;

// Load all frames, use file-declared axis order
let result = load_bvh("motion.bvh", -1, None)?;

// Load only first 100 frames, force ZXY axis order
let result = load_bvh("motion.bvh", 100, Some("ZXY"))?;
```

### Return Value: BvhLoadResult

Return type of `load_bvh`, containing the fully parsed skeleton data.

```rust
pub struct BvhLoadResult {
    pub frames: Vec<Frame>,  // Vec<HashMap<String, HumanPose>>
    pub fps: u32,
}
```

Joint names come from parsing the `JOINT` node names in the BVH HIERARCHY section.

#### Methods

| Method | Return | Description |
|--------|--------|-------------|
| `frame_count()` | `usize` | Number of frames |
| `duration_secs()` | `f64` | Duration in seconds |
| `hierarchy()` | `Vec<(&str, Option<&str>)>` | Parent-child joint pairs, `None` = root |
| `joint_pose(frame_idx, joint)` | `Option<&HumanPose>` | Pose of specified joint at specified frame |

#### Skeleton Structure Example

`hierarchy()` is parsed from the `JOINT` nodes in the BVH HIERARCHY section, directly reflecting the source file's skeleton structure:

```rust
let result = load_bvh("motion.bvh", -1, Some("ZXY"))?;

println!("Frame rate: {} fps", result.fps);
println!("Frame count: {}", result.frame_count());
println!("Duration: {:.2}s", result.duration_secs());

// hierarchy() returns (joint_name, parent_name) pairs; None = root node
for (joint, parent) in result.hierarchy() {
    println!("{} <- {:?}", joint, parent);
}
```

Example output (hierarchy of a standard skeleton BVH):

```
ROOT Hips                # Pelvis, root node
├── Spine                # Lower spine
│   └── Spine1            # Middle spine
│       └── Spine2        # Upper spine
│           └── Spine3    # Chest
│               ├── Neck
│               │   └── Head
│               ├── LeftShoulder
│               │   └── LeftArm          # Left upper arm
│               │       └── LeftForeArm  # Left forearm
│               │           └── LeftHand  # Left hand
│               └── RightShoulder
│                   └── RightArm
│                       └── RightForeArm
│                           └── RightHand
├── LeftUpLeg            # Left thigh
│   └── LeftLeg           # Left shin
│       └── LeftFoot      # Left ankle
│           └── LeftToe   # Left toes
└── RightUpLeg
    └── RightLeg
        └── RightFoot
            └── RightToe
```

Different BVH files have different joint names — `hierarchy()` faithfully returns whatever the source file declares.

#### Query Pose of a Specific Joint

```rust
// Query Hips pose at frame 0
if let Some(pose) = result.joint_pose(0, "Hips") {
    println!("Hips position: {:?}", pose.pos);
    println!("Hips rotation: {:?}", pose.quat_wxyz);
}
```

---

## export_bvh_file

Normalize and re-export a BVH file with specified axis order and target frame rate.

```rust
pub fn export_bvh_file(
    input_bvh_path: &str,
    output_bvh_path: &str,
    axis_order: &str,
    target_fps: Option<u32>,
) -> Result<()>
```

### Example

```rust
use ee_sdk::ee_core::loader::bvh::export_bvh_file;

// Convert to ZXY axis order, output at 30fps
export_bvh_file("input.bvh", "output_normalized.bvh", "ZXY", Some(30))?;

// Keep original frame rate, only change axis order
export_bvh_file("input.bvh", "output_zxy.bvh", "ZXY", None)?;
```

---

## convert_bvh_to_retarget_bvh

Preserve original hierarchy, only normalize rotation channel order.

```rust
pub fn convert_bvh_to_retarget_bvh(
    input_bvh_path: &str,
    output_bvh_path: &str,
    axis_order: Option<&str>,
    fps: Option<u32>,
) -> bool
```

Returns `true` on success.

### Example

```rust
let ok = ee_sdk::convert_bvh_to_retarget_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    "good_time_normalized.bvh",
    Some("ZXY"),
    None,
);
assert!(ok);
```

---

## Standard Bvh

Standard skeleton wrapper. Frame data guarantees the complete 24-joint skeleton. Missing joints are filled by copying the parent pose.

### StandardBvh

```rust
pub struct StandardBvh {
    pub fps: u32,
    pub frames: Vec<HashMap<String, HumanPose>>,
}
```

### StandardBvhSummary

```rust
pub struct StandardBvhSummary {
    pub fps: u32,
    pub frame_count: usize,
}
```

### Functions

| Function | Description |
|----------|-------------|
| `load_standard_bvh(path)` | Load standard skeleton BVH |
| `save_standard_bvh(bvh, path)` | Export to standard BVH file |
| `convert_bvh_to_standard_bvh(in, out)` | One-stop conversion: load → normalize → export |

### Example

```rust
use ee_sdk::standard_bvh::{load_standard_bvh, save_standard_bvh, convert_bvh_to_standard_bvh};

// Load standard skeleton (auto handles joint name mapping and missing joint filling)
let bvh = load_standard_bvh("motion.bvh")?;
save_standard_bvh(&bvh, "standard_motion.bvh")?;

// One-stop conversion
let summary = convert_bvh_to_standard_bvh(
    "data/jobs/source/bvh/good_time.bvh",
    "good_time_standard.bvh",
)?;
println!("fps={}, frames={}", summary.fps, summary.frame_count);
```
