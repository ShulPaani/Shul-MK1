# 🦾 SHUL MK-1 — Day 6: URDF Mesh Origin Fix & Full Visual Model in RViz

## 📅 Day 6 Progress Log

> **Goal:** Fix broken joint behavior in RViz where links were flying out of place during joint slider movement, and add actual STL mesh visuals to the URDF (Day 5 only had TF frames).

---

## 🔁 Quick Recap — What Was Day 5?

On **Day 5**, the URDF had only **TF (Transform) frames** — meaning RViz was showing colored coordinate axes (red/green/blue arrows) at each joint, but **zero mesh geometry**. The arm structure existed mathematically but was completely invisible as a 3D model.

**Day 5 state:**
- ✅ Joint hierarchy defined (base → shoulder → upper_arm → forearm → wrist_bend → wrist_rotate)
- ✅ TF tree correct — all frames visible in RViz
- ❌ No `<mesh>` tags — no STL files loaded
- ❌ No visual geometry at all

---

## 🎯 Day 6 — What I Tried to Fix

### Step 1: Added STL Meshes to All Links

Added `<visual>` blocks with `<mesh>` tags pointing to exported STL files from Fusion 360:

```xml
<link name="upper_arm_link">
  <visual>
    <geometry>
      <mesh filename="file:///mnt/c/Users/asmit/Downloads/robot_arm/meshes/upper_arm_link.stl" 
            scale="0.001 0.001 0.001"/>
    </geometry>
  </visual>
</link>
```

> **Note:** `scale="0.001 0.001 0.001"` is used because Fusion 360 exports in **millimeters**, but ROS2/RViz works in **meters**.

---

## 🐛 The Main Bug — Links Flying Out on Joint Slider Movement

### What was happening:

When I moved any joint slider in RViz's `joint_state_publisher_gui`, the child link would **detach and fly away** from its parent instead of rotating around the joint pivot cleanly.

**Before fix (rest pose):**

> 📸 *[Insert RViz screenshot — arm in upright rest pose]*

**After moving shoulder slider:**

> 📸 *[Insert RViz screenshot — upper arm flying diagonally away from base]*

---

### 🔍 Root Cause Analysis

I initially thought the problem was in the **URDF joint `xyz` values** being wrong — spent time tuning offsets, trying different Z values, adjusting X offsets. Nothing worked cleanly.

**The actual problem was completely different:**

> **The mesh origin (0,0,0 point) of each STL component was NOT at the servo axle/pivot center.**

When Fusion 360 exports an STL, it uses whatever the **component's local origin** is as the mesh origin. In my case, the component origins were sitting at random corners or endpoints of the geometry — **not at the joint pivot axis.**

This means:
- URDF says: *"rotate this link around point X"*
- The mesh spawns with its origin at point X
- But the **servo axle** is physically somewhere else in the mesh
- Result: rotation looks correct mathematically, but visually the mesh swings wildly

```
❌ WRONG (what was happening):        ✅ CORRECT (what should happen):

   [mesh geometry]                       [mesh geometry]
         |                                     ↑
         |                               origin = servo axle
         |                               joint rotates HERE
    origin here
    (corner of mesh)
    joint rotates HERE
    → mesh flies away
```

---

## 🔧 The Fix — Moving Component Origin to Servo Axle in Fusion 360

### Method Used: Fusion 360 Component Origin Repositioning

For each link component:

1. **Open Fusion 360** → Open ROBO_ARM V1 design file
2. **Double-click** the component to enter Edit Component mode
3. **Identify the servo axle center** — the hole/shaft where the joint physically rotates
4. Use **Inspect → Measure (`I`)** to find the exact coordinates of the axle center
5. **Modify → Move/Copy** the component origin to align with the servo axle center
   - Or: Right-click → Move/Copy → select "Point to Point" → snap origin to axle center
6. **Re-export as STL** → File → Export → STL (replace old file)
7. Repeat for all affected links

### Links Fixed:
| Link | Problem | Fix |
|------|---------|-----|
| `shoulder_link` | Origin at base of mesh | Moved to shoulder servo axle center |
| `upper_arm_link` | Origin at end of arm | Moved to shoulder-side servo axle |
| `forearm_link` | Origin at random point | Moved to elbow servo axle center |
| `wrist_bend_link` | Origin offset from pivot | Moved to wrist servo axle center |
| `wrist_rotate_link` | Origin misaligned | Moved to wrist rotate axle center |

---

## 📄 Final URDF — Day 6

```xml
<?xml version="1.0"?>
<robot name="robot_arm">

  <!-- ================= LINKS ================= -->

  <link name="base_link">
    <visual>
      <geometry>
        <mesh filename="file:///mnt/c/Users/asmit/Downloads/robot_arm/meshes/base_link.stl" 
              scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="shoulder_link">
    <visual>
      <geometry>
        <mesh filename="file:///mnt/c/Users/asmit/Downloads/robot_arm/meshes/shoulder_link.stl" 
              scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="upper_arm_link">
    <visual>
      <geometry>
        <mesh filename="file:///mnt/c/Users/asmit/Downloads/robot_arm/meshes/upper_arm_link.stl" 
              scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="forearm_link">
    <visual>
      <geometry>
        <mesh filename="file:///mnt/c/Users/asmit/Downloads/robot_arm/meshes/forearm_link.stl" 
              scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="wrist_bend_link">
    <visual>
      <geometry>
        <mesh filename="file:///mnt/c/Users/asmit/Downloads/robot_arm/meshes/wrist_bend_link.stl" 
              scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="wrist_rotate_link">
    <visual>
      <geometry>
        <mesh filename="file:///mnt/c/Users/asmit/Downloads/robot_arm/meshes/wrist_rotate_link.stl" 
              scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <!-- ================= JOINTS ================= -->

  <!-- Base turntable — rotates around Z (yaw) -->
  <joint name="base_to_shoulder" type="revolute">
    <parent link="base_link"/>
    <child link="shoulder_link"/>
    <origin xyz="-0.02 0 0.05" rpy="0 0 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="-3.14" upper="3.14" effort="10" velocity="1"/>
  </joint>

  <!-- Shoulder pitch joint — upper arm swings forward/backward -->
  <joint name="shoulder_to_upper" type="revolute">
    <parent link="shoulder_link"/>
    <child link="upper_arm_link"/>
    <origin xyz="-0.01 0 0.08" rpy="0 1.57 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="-1.57" upper="1.57" effort="10" velocity="1"/>
  </joint>

  <!-- Elbow pitch joint -->
  <joint name="upper_to_forearm" type="revolute">
    <parent link="upper_arm_link"/>
    <child link="forearm_link"/>
    <origin xyz="-0.120 0 0.027" rpy="0 0 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="-1.57" upper="1.57" effort="10" velocity="1"/>
  </joint>

  <!-- Wrist bend joint -->
  <joint name="forearm_to_wrist_bend" type="revolute">
    <parent link="forearm_link"/>
    <child link="wrist_bend_link"/>
    <origin xyz="-0.10001 0 0.001" rpy="3.14 0 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="-1.57" upper="1.57" effort="10" velocity="1"/>
  </joint>

  <!-- Wrist rotate (end-effector roll) -->
  <joint name="wrist_bend_to_wrist_rotate" type="revolute">
    <parent link="wrist_bend_link"/>
    <child link="wrist_rotate_link"/>
    <origin xyz="-0.07 0 0.023" rpy="3.14 1.57 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="-3.14" upper="3.14" effort="10" velocity="1"/>
  </joint>

</robot>
```

---

## ✅ Result After Fix

> 📸 *[Insert RViz screenshot — arm in correct rest pose with all meshes visible]*

> 📸 *[Insert RViz screenshot — arm moving correctly with joint sliders]*

- All 6 links visible with correct STL meshes ✅
- Joint sliders move each link correctly around its pivot ✅
- No links flying out of place ✅
- Full 6-DOF arm visible in RViz ✅

---

## 💡 Key Learnings

### 1. Mesh Origin ≠ Joint Pivot (most common URDF beginner mistake)
The STL mesh origin **must be placed at the joint's rotation axis** in your CAD software before exporting. If it's anywhere else, the math is wrong no matter how you tune URDF values.

### 2. Don't Guess — Measure
Used Fusion 360's **Inspect → Measure (`I`)** tool to find exact servo axle coordinates instead of trial-and-error tuning of URDF xyz values.

### 3. Fusion 360 Export Workflow
```
Edit Component → Move Origin to Servo Axle → Export STL → Replace in meshes folder
```

### 4. scale="0.001 0.001 0.001" is mandatory
Fusion exports in mm, ROS2 works in meters. Always scale down by 1000.

---

## 🔜 Day 7 Plan

- [ ] Add `<collision>` geometry to all links
- [ ] Add `<inertial>` properties for MoveIt2 compatibility
- [ ] Test with MoveIt2 Setup Assistant
- [ ] Begin motion planning pipeline

---

## 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| Fusion 360 | CAD design + STL export |
| ROS2 Jazzy | Robot framework |
| RViz2 | 3D visualization |
| joint_state_publisher_gui | Joint slider testing |
| urdf_tutorial | URDF display launch file |

---
