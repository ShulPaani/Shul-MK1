# Day 2 — Full Assembly of Shul MK-1

## Overview
Today marked a major milestone — the **Shul MK-1 6 DOF robotic arm** was fully assembled for the first time. After weeks of CAD design in Fusion 360 and 3D printing in PETG, all components came together into a working physical structure.

---

## ✅ What Was Accomplished

- Assembled all 3D printed PETG components onto the base
- Mounted **3x MG996R** servo motors at shoulder, elbow, and base rotation joints
- Mounted **3x MG90S** servo motors at wrist bend, wrist rotation, and gripper joints
- Fitted **1x 624ZZ ball bearing** at elbow joint for smooth rotation
- Routed all servo wires through the arm structure
- Verified smooth manual motion across all joints
- Confirmed the **"SHUL MK-1"** base engraving is intact post-print

---

## ⚠️ Issues Encountered

### 1. Gripper Servo
- One MG90S servo at the gripper joint failed during assembly
- **Resolution:** Gripper module temporarily removed; replacement servo ordered
- Gripper will be reattached once the new servo arrives

### 2. Second Ball Bearing
- The second 624ZZ bearing could not be fitted at the U-bracket end due to design clearance constraints
- **Resolution:** Joint running without bearing on that side for V1; full dual-bearing support planned for V2 redesign

### 3. Weight Distribution
- Identified a **weight imbalance** at the upper arm — arm tends to shift under its own load
- **Root cause:** Single-sided servo horn attachment without counterbalance support on the opposite end
- **Planned fix:** Adding bearing support on idle side in V2; temporary counterweight being evaluated for V1

---

## 🔧 Key Learnings

- PETG shrinks ~0.2mm post-print — accounting for this in hole diameters proved critical
- Cyanoacrylate adhesive has poor adhesion on PETG — mechanical fasteners preferred
- Soldering iron can be carefully used to create wire relief notches in printed parts
- Center all servos to **90°** before assembly to avoid mechanical binding
- Always design dual bearing support on both sides of a joint for stability

---

## 📸 Photos

> 
<img width="960" height="1280" alt="WhatsApp Image 2026-04-18 at 9 42 00 PM" src="https://github.com/user-attachments/assets/aff339f4-a8db-4ab2-b1c0-00671798cc70" />

**Base + Shoulder + Upper-Arm**
  
<img width="960" height="1280" alt="WhatsApp Image 2026-04-18 at 9 42 14 PM" src="https://github.com/user-attachments/assets/0087162d-7e7b-49c1-ae29-3085a48a6832" />

**Elbow**

<img width="960" height="1280" alt="WhatsApp Image 2026-04-18 at 9 42 10 PM" src="https://github.com/user-attachments/assets/f9ff0577-208b-4338-86a0-268634695bcf" />

**Wrist + Gripper**

<img width="899" height="1599" alt="WhatsApp Image 2026-04-18 at 1 52 22 PM" src="https://github.com/user-attachments/assets/fb436f85-2323-4686-bc71-48da98d18b57" />

**Fully Assembled**


---

## ⏭️ Next Steps — Day 3

- Wire PCA9685 16-channel PWM driver to Arduino
- Test all 5 active servos via PCA9685
- Write basic sweep test code

---

## 🛠️ Hardware Used Today

| Component | Quantity |
|-----------|----------|
| MG996R Servo | 3 |
| MG90S Servo | 3 (1 failed) |
| 624ZZ Ball Bearing | 1 |
| PETG Printed Parts | 8 |

---

*Shul MK-1 — Original 6 DOF Robotic Arm Design*  
*Designed from scratch in Fusion 360 | Printed in PETG*
