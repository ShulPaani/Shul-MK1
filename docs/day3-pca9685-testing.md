

# Day 3 — Servo Testing & Serial Control via PCA9685

## Overview
With the arm physically assembled, Day 3 focused on bringing **Shul MK-1 to life electronically**. All 5 active servo motors were tested using the **PCA9685 16-channel PWM driver** wired to an **Arduino**. A serial command interface was implemented to control each joint independently in real time. 

---

## ✅ What Was Accomplished

- Wired **PCA9685** to Arduino via I2C (SDA, SCL)
- Connected all 5 active MG996R and MG90S servos to PCA9685 channels
- Powered servos via external 5V supply (not Arduino 5V — to avoid brownout)
- Wrote serial command-based control code — each joint controllable independently
- Successfully moved all 5 joints to custom angles via Serial Monitor
- Confirmed no mechanical binding or collision during full range of motion
- Verified PCA9685 I2C communication stable throughout testing

---

## 🔌 Wiring Setup

| PCA9685 Pin | Arduino Pin |
|-------------|-------------|
| VCC | 5V |
| GND | GND |
| SDA | A4 |
| SCL | A5 |

> ⚠️ Servo power (V+) connected to external 5V supply — NOT Arduino 5V pin

### Servo Channel Mapping

| Channel | Joint | Servo |
|---------|-------|-------|
| 0 | Base Rotation | MG996R |
| 1 | Shoulder | MG996R |
| 2 | Elbow | MG996R |
| 3 | Wrist | MG90S |
| 4 | Gripper | MG90S |

---

## 💻 Code

```cpp
#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>

Adafruit_PWMServoDriver pca = Adafruit_PWMServoDriver();

#define SERVO_MIN 150
#define SERVO_MAX 600

// Channels
#define BASE     0
#define SHOULDER 1
#define ELBOW    2
#define WRIST    3
#define GRIPPER  4

int angleToPulse(int angle) {
  return map(angle, 0, 180, SERVO_MIN, SERVO_MAX);
}

void moveServo(int channel, int angle) {
  angle = constrain(angle, 0, 180);
  pca.setPWM(channel, 0, angleToPulse(angle));
}

void setup() {
  Serial.begin(9600);
  pca.begin();
  pca.setPWMFreq(50);
  Serial.println("Ready for commands!");
}

void loop() {
  if (Serial.available()) {
    char joint = Serial.read();
    int angle = Serial.parseInt();
    switch (joint) {
      case 'B': moveServo(BASE, angle);     break;
      case 'S': moveServo(SHOULDER, angle); break;
      case 'E': moveServo(ELBOW, angle);    break;
      case 'W': moveServo(WRIST, angle);    break;
      case 'G': moveServo(GRIPPER, angle);  break;
      default: Serial.println("Invalid command");
    }
    Serial.print("Moved ");
    Serial.print(joint);
    Serial.print(" to ");
    Serial.println(angle);
  }
}
```

---

## 🕹️ How to Use

Open Arduino Serial Monitor at **9600 baud** and send commands:

| Command | Action |
|---------|--------|
| `B90` | Base → 90° |
| `S45` | Shoulder → 45° |
| `E120` | Elbow → 120° |
| `W90` | Wrist → 90° |
| `G45` | Gripper → 45° |

---

## 🔧 Key Learnings

- PCA9685 must be powered via **external supply** — drawing servo current from Arduino causes voltage drop and resets
- `constrain()` used to prevent out-of-range angles from damaging servos
- `Serial.parseInt()` cleanly parses angle values from serial input
- I2C address of PCA9685 defaults to **0x40**
- All joints should be at **90° before testing** to avoid mechanical shock on first run

---

## 📸 Photos/Videos

> 
> https://github.com/user-attachments/assets/094fc488-1984-4f30-84be-585e17740919
>
> https://github.com/user-attachments/assets/4a883fe6-9bec-47fe-84d6-8a33e531654c
> 
> https://github.com/user-attachments/assets/8d8d1b6d-c379-405c-8f36-e1f633eeab53
> 
> https://github.com/user-attachments/assets/8969c16e-1393-4764-8e39-82d73d77cde0
> 























---

## ⚠️ Known Issues

- Gripper servo still pending — replacement MG90S ordered
- Weight imbalance at upper arm noted — being evaluated for V2

---

## ⏭️ Next Steps — Day 4

- Implement motion control with potentiometers
- Fine tune SERVO_MIN/SERVO_MAX per channel for accurate positioning

---

## 🛠️ Hardware Used

| Component | Detail |
|-----------|--------|
| Arduino | Uno / Nano |
| PCA9685 | 16-ch PWM Driver, I2C |
| MG996R | 3x — Channels 0,1,2 |
| MG90S | 2x active — Channels 3,4 |
| External PSU | 5V for servo power |

---

*Shul MK-1 — Original 6 DOF Robotic Arm Design*
