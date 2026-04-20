# Day 5 — Manual 4-DOF Control via Potentiometers

## Overview
Day 5 introduced **real-time manual control** of Shul MK-1 using 4 potentiometers — one per joint. Each potentiometer directly maps to a servo channel on the PCA9685, allowing intuitive physical control of the arm's 4 primary joints. The system runs on **Arduino Nano** powered by the **SMPS 5V 5A** supply.

---

## ✅ What Was Accomplished

- Wired 4 potentiometers to Arduino Nano analog pins A0–A3
- Mapped each pot to a corresponding PCA9685 servo channel
- Implemented **deadband threshold** to eliminate servo jitter from ADC noise
- All 4 servos responding smoothly and accurately to pot input
- Serial monitor logging added for real-time debugging
- SMPS 5V 5A powering all servos stably via PCA9685 V+ rail

---

## 🔌 Wiring Setup

### Potentiometers → Arduino Nano

| Pot | Arduino Pin | Joint |
|-----|-------------|-------|
| Pot 1 | A0 | Base Rotation |
| Pot 2 | A1 | Shoulder |
| Pot 3 | A2 | Elbow |
| Pot 4 | A3 | Wrist |

### Power Setup

| Source | Connected To |
|--------|-------------|
| SMPS 5V (+) | PCA9685 V+ |
| SMPS GND (-) | PCA9685 GND + Arduino GND |
| Arduino Nano | USB / VIN |

> ⚠️ Common ground between SMPS and Arduino is essential for stable operation

---

## 💻 Code

```cpp
#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>

Adafruit_PWMServoDriver pwm = Adafruit_PWMServoDriver();

// Potentiometer analog input pins
int potPins[] = {A0, A1, A2, A3};
int currentPos[4];
int threshold = 3; // Deadband threshold to prevent jitter

void setup() {
  Serial.begin(9600);
  pwm.begin();
  pwm.setPWMFreq(50); // Standard frequency for analog servos
  Serial.println("--- 4-DOF MANUAL CONTROL READY ---");
}

void loop() {
  for (int i = 0; i < 4; i++) {
    int potVal = analogRead(potPins[i]);
    // Map pot range (0-1023) to PCA9685 pulse width (150-600)
    int target = map(potVal, 0, 1023, 150, 600);
    // Only update servo if pot moved beyond deadband threshold
    if (abs(target - currentPos[i]) > threshold) {
      currentPos[i] = target;
      pwm.setPWM(i, 0, target);
      Serial.print("Servo "); Serial.print(i);
      Serial.print(": "); Serial.println(target);
    }
  }
  delay(15); // Small delay for smooth servo performance
}
```

---

## 🕹️ How It Works

```
Pot rotated
    ↓
analogRead() → 0 to 1023
    ↓
map() → 150 to 600 (PCA9685 pulse width)
    ↓
Deadband check (threshold = 3)
    ↓
pwm.setPWM() → Servo moves ✅
```

---

## 🔧 Key Learnings

- **Deadband threshold** is critical — without it ADC noise causes constant servo jitter even when pot is stationary
- `map()` function cleanly handles pot-to-pulse conversion
- SMPS common ground with Arduino is **mandatory** — floating ground causes erratic servo behavior
- `delay(15)` gives servos enough time to reach position before next update cycle
- If a servo moves in reverse — swap `150` and `600` in the `map()` function

---

## 📸 Photos/Videos

> *(Add potentiometer control demo video here)*

---

## ⚠️ Known Issues

- Gripper servo still pending — replacement MG90S ordered
- 5th joint (wrist rotation) not yet included in pot control — will be added in next iteration

---

## ⏭️ Next Steps — Day 6

- Add 5th potentiometer for wrist rotation
- Implement smooth interpolated motion
- Begin ROS2 setup and URDF planning for Shul MK-1

---

## 🛠️ Hardware Used

| Component | Detail |
|-----------|--------|
| Arduino Nano | Microcontroller |
| PCA9685 | 16-ch PWM Driver, I2C |
| Potentiometers | 4x 10kΩ |
| MG996R | 3x |
| MG90S | 1x active |
| SMPS | 5V 5A |

---

*Shul MK-1 — Original 6 DOF Robotic Arm Design*
*Designed from scratch in Fusion 360 | Printed in PETG*
