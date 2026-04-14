---
layout: default
title: Core Concepts
parent: Rust API
nav_order: 1
lang: en
ref: rust-api-core-types
permalink: /en/docs/reference/rust/core-types.html
---

# Core Concepts

---

## BVH Basics

BVH (Biovision Hierarchy) is a universal human animation file format supported by nearly all motion capture software and 3D packages.

A BVH file has two parts:

| Part | Content |
|------|---------|
| **HIERARCHY** | Skeleton structure: joint names, parent-child relationships, bone lengths (OFFSET) |
| **MOTION** | Animation data: rotation and position values per joint per frame |

### Bone Tree

BVH describes the skeleton as a tree. The root node is **Hips**, which records the body's world position; child joints record rotation relative to their parent.

```
ROOT Hips
├── Spine
│   └── Spine1 → Spine2 → Spine3
│       ├── LeftShoulder → LeftArm → LeftForeArm → LeftHand
│       └── RightShoulder → RightArm → RightForeArm → RightHand
├── LeftUpLeg → LeftLeg → LeftFoot → LeftToe
└── RightUpLeg → RightLeg → RightFoot → RightToe
```

### Rotation Representation

BVH stores rotations as **Euler angles** with order **Z → X → Y**.

- **Z axis**: Roll — controls body tilt left/right
- **X axis**: Pitch — controls body tilt forward/backward
- **Y axis**: Yaw — controls body rotation left/right

### Coordinate System

- **Y axis up**, **Z axis forward**
- **Units**: length in cm, time in seconds

---

## Standardization

### Why Standardize

BVH files from different sources vary wildly:

| Difference | Source A | Source B |
|------------|----------|----------|
| Joint naming | `Hips`, `Spine`, `L_UpLeg` | `pelvis`, `back`, `left_thigh` |
| Skeleton hierarchy | Full 24 joints | Missing shoulder, only 18 joints |
| Bone length | Adult average 170cm | Child 120cm |
| Rotation order | Z-X-Y | X-Z-Y |

Mixing them directly causes distorted or misaligned retargeting results. **Standardization** unifies joint naming, skeleton structure (24 joints), bone length reference, and rotation order.

### Standard Skeleton (24 Joints)

All input data is ultimately mapped to a uniform 24-joint skeleton:

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

### T-Pose

The T-Pose is the zero-pose reference for all motion data: both arms extended sideways at approx. ±90° around the X axis, legs straight and together, feet pointing +Z, spine straight.

### Standard Skeleton → Unitree G1 Joint Mapping

| Standard Joint | Unitree G1 Joint | Description |
|---------------|-----------------|-------------|
| Hips | `pelvis` | Pelvis, root node |
| Spine | — | No G1 independent spine joint, constrained via torso |
| Spine2 | `torso_link` | Torso chain, G1 main spine |
| LeftArm | `left_shoulder_yaw_link` | Left shoulder |
| LeftForeArm | `left_elbow_link` | Left elbow |
| LeftHand | `left_wrist_yaw_link` | Left wrist |
| RightArm | `right_shoulder_yaw_link` | Right shoulder |
| RightForeArm | `right_elbow_link` | Right elbow |
| RightHand | `right_wrist_yaw_link` | Right wrist |
| LeftUpLeg | `left_hip_yaw_link` | Left hip |
| LeftLeg | `left_knee_link` | Left knee |
| LeftFootMod | `left_ankle_roll_link` | Left ankle |
| RightUpLeg | `right_hip_yaw_link` | Right hip |
| RightLeg | `right_knee_link` | Right knee |
| RightFootMod | `right_ankle_roll_link` | Right ankle |

{: .note }
> Spine1, Spine3, Neck, Head, LeftShoulder, RightShoulder, LeftFoot, RightFoot, LeftToe, RightToe are not involved in G1 retargeting but are preserved in Standard BVH.

---

## Target Robot

### Robot

Target robot type. Currently only Unitree G1 is supported.

```rust
Robot::UnitreeG1
```

### load_robot_description

Load and parse a robot model description file. Supports both URDF and MJCF formats, auto-detected by file extension:

| File Extension | Format |
|----------------|--------|
| `.xml` | MJCF  |
|`.urdf` | URDF |

```rust
pub fn load_robot_description(
    robot: Robot,
    robot_file: Option<&str>,
) -> Result<RobotDescription>
```

| Param | Type | Description |
|-------|------|-------------|
| `robot` | `Robot` | Target robot |
| `robot_file` | `Option<&str>` | Custom model file path; `None` uses the built-in default |

```rust
use ee_sdk::prelude::*;

let desc = load_robot_description(Robot::UnitreeG1, None)?;
// Specify a custom MJCF file
let desc = load_robot_description(Robot::UnitreeG1, Some("path/to/robot.xml"))?;
```

### RobotDescription

| Field | Type | Description |
|-------|------|-------------|
| `robot_name` | `String` | Robot name |
| `root_link` | `String` | Root link name |
| `dof_count` | `usize` | Degree of freedom count |
| `dof_names` | `Vec<String>` | DOF name list |
| `joints` | `Vec<JointDescription>` | Joint description list |

### JointDescription

| Field | Type | Description |
|-------|------|-------------|
| `name` | `String` | Joint name |
| `joint_type` | `String` | Joint type (revolute / prismatic, etc.) |
| `parent_link` | `String` | Parent link name |
| `child_link` | `String` | Child link name |
| `axis` | `[f64; 3]` | Rotation axis direction vector |
| `lower_limit` | `f64` | Position lower limit |
| `upper_limit` | `f64` | Position upper limit |
| `velocity_limit` | `f64` | Maximum velocity |

---

## API Types

### Prelude

Rust's `prelude` pattern: commonly-used types are grouped in a `prelude` module and imported with a single statement.

```rust
use ee_sdk::prelude::*;
```

Includes: `Robot`, `RetargetOutput`, `RetargetSession`, `BvhRetargetOptions`, `RetargetConfig`, `resample_frames`, `STANDARD_FPS`, `MIN_INPUT_FPS`, `MAX_INPUT_FPS`, `StandardBvh`, `StandardBvhSummary`, `load_robot_description`, `RobotDescription`, `JointDescription`, `SkeletonData`, `supported_robots`, `supported_source_humans`, `load_bvh_skeleton_snapshot`

### HumanPose

Pose of a single joint at a single frame:

```rust
pub struct HumanPose {
    pub pos: [f64; 3],        // Position (m). Root joint: world coordinates. Child joints: offset from parent
    pub quat_wxyz: [f64; 4], // Quaternion (w, x, y, z). Rotation relative to parent joint
}
```

### Frame

Pose of all joints at one frame, keyed by joint name:

```rust
type Frame = HashMap<String, HumanPose>;
```

`BvhLoadResult.frames` returned by `load_bvh` is `Vec<Frame>`.

### FrameOutput

Output of `RetargetSession::retarget_frame()` for a single frame:

| Field | Type | Description |
|-------|------|-------------|
| `root_pos` | `[f64; 3]` | Root position (m) |
| `root_rot_xyzw` | `[f64; 4]` | Root rotation quaternion (xyzw) |
| `dof_pos` | `Vec<f64>` | Joint DOF values, in robot DOF order |

### RetargetOutput

Full output of `retarget_from_bvh`:

| Field | Type | Description |
|-------|------|-------------|
| `frames` | `Vec<FrameOutput>` | Per-frame robot joint values |
| `robot` | `Robot` | Target robot type |
