# Shul MK-1 - Day 5: ROS2 Implementation Begins 🤖

## 📅 Day 5 Progress

Today marks the official start of my ROS2 journey with the Shul MK-1 robotic arm! After weeks of mechanical design in Fusion 360, I've successfully:

- ✅ Converted Fusion 360 assembly to URDF format
- ✅ Exported all links as STL meshes
- ✅ Configured joint parameters and transforms
- ✅ Implemented 5 DOF (gripper coming soon!) 

## 🗂️ Project Structure
<img width="383" height="285" alt="Screenshot 2026-04-24 012016" src="https://github.com/user-attachments/assets/3fd10d5d-cd89-4a5d-bd7d-02ed31ca9363" />


## 🤖 Current DOF Configuration

| Joint | Type | Axis | Range (rad) |
|-------|------|------|-------------|
| base_to_shoulder | Revolute | Z | ±3.14 |
| shoulder_to_upper | Revolute | Y | ±1.57 |
| upper_to_forearm | Revolute | Y | ±1.57 |
| forearm_to_wrist_bend | Revolute | Y | ±1.57 |
| wrist_bend_to_wrist_rotate | Revolute | Z | ±3.14 |

**Note:** 6th DOF (gripper) will be added in upcoming days!

## 🏗️ Mechanical Build

> <img width="619" height="711" alt="Screenshot 2026-04-24 012748" src="https://github.com/user-attachments/assets/3eeb553b-9b10-4172-99a2-1029560fdea3" />


> <img width="651" height="687" alt="Screenshot 2026-04-24 012806" src="https://github.com/user-attachments/assets/42613304-3cea-4d49-b1b6-588e3dbb6f8c" />

*Fusion 360 design exported to URDF with accurate joint placements*

## 🖥️ RViz Visualization

> <img width="1916" height="1016" alt="Screenshot 2026-04-24 013300" src="https://github.com/user-attachments/assets/bcf5915f-754f-42fc-8d04-5074bf4281c7" />
- Robot arm in RViz with all 5 active joints

> <img width="1909" height="1012" alt="Screenshot 2026-04-24 013400" src="https://github.com/user-attachments/assets/9193ae86-dbe7-44cb-9739-2ebd79661bd3" />
- Joint limit testing and transform visualization

## 📝 Complete URDF Code

```xml
<?xml version="1.0"?>
<robot name="robot_arm">

  <!-- ================= LINKS ================= -->

  <link name="base_link">
    <visual>
      <geometry>
        <mesh filename="../meshes/base_link.stl" scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="shoulder_link">
    <visual>
      <geometry>
        <mesh filename="../meshes/shoulder_link.stl" scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="upper_arm_link">
    <visual>
      <geometry>
        <mesh filename="../meshes/upper_arm_link.stl" scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="forearm_link">
    <visual>
      <geometry>
        <mesh filename="../meshes/forearm_link.stl" scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="wrist_bend_link">
    <visual>
      <geometry>
        <mesh filename="../meshes/wrist_bend_link.stl" scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <link name="wrist_rotate_link">
    <visual>
      <geometry>
        <mesh filename="../meshes/wrist_rotate_link.stl" scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>

  <!-- ================= JOINTS ================= -->

  <joint name="base_to_shoulder" type="revolute">
    <parent link="base_link"/>
    <child link="shoulder_link"/>
    <origin xyz="0 0 0.05" rpy="0 0 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="-3.14" upper="3.14" effort="10" velocity="1"/>
  </joint>

  <joint name="shoulder_to_upper" type="revolute">
    <parent link="shoulder_link"/>
    <child link="upper_arm_link"/>
    <origin xyz="0 0 0.1" rpy="0 0 0"/>
    <axis xyz="0 1 0"/>
    <limit lower="-1.57" upper="1.57" effort="10" velocity="1"/>
  </joint>

  <joint name="upper_to_forearm" type="revolute">
    <parent link="upper_arm_link"/>
    <child link="forearm_link"/>
    <origin xyz="0 0 0.1" rpy="0 0 0"/>
    <axis xyz="0 1 0"/>
    <limit lower="-1.57" upper="1.57" effort="10" velocity="1"/>
  </joint>

  <joint name="forearm_to_wrist_bend" type="revolute">
    <parent link="forearm_link"/>
    <child link="wrist_bend_link"/>
    <origin xyz="0 0 0.08" rpy="0 0 0"/>
    <axis xyz="0 1 0"/>
    <limit lower="-1.57" upper="1.57" effort="10" velocity="1"/>
  </joint>

  <joint name="wrist_bend_to_wrist_rotate" type="revolute">
    <parent link="wrist_bend_link"/>
    <child link="wrist_rotate_link"/>
    <origin xyz="0 0 0.05" rpy="0 0 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="-3.14" upper="3.14" effort="10" velocity="1"/>
  </joint>

</robot>
