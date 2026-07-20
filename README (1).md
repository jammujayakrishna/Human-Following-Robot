# 🤖 Human Following Robot

A 4-wheeled Arduino robot that detects a person in front of it and follows them — steering left or right to stay centered, stopping when it gets close, and sweeping its ultrasonic sensor to search when it loses track.

Built on an **Arduino Uno**, an **L298N** motor driver, two **IR obstacle sensors**, an **HC-SR04 ultrasonic sensor**, and a **servo** that pans the ultrasonic sensor to scan for a target.

---

## ✨ Features

- **Distance-based following** — keeps a set gap from the person using the ultrasonic sensor
- **Left/right steering** — two IR sensors detect which side the person has drifted to
- **Auto-stop** — halts when the person gets closer than a safe distance
- **Search mode** — sweeps the servo left/right to relocate the person if they're lost
- **Fully tunable** — speeds and distance thresholds are simple variables at the top of the code

---

## 🧰 Hardware used

| Component | Qty |
|---|---|
| Arduino Uno R3 | 1 |
| L298N dual H-bridge motor driver | 1 |
| DC gear motors + mecanum/rubber wheels | 4 |
| HC-SR04 ultrasonic distance sensor | 1 |
| IR obstacle avoidance sensor | 2 |
| SG90 (or similar) servo motor | 1 |
| 12V battery pack (e.g. 3x 18650) | 1 |
| Jumper wires, chassis, mounting hardware | — |

---

## 🔌 Circuit diagram

![Wiring diagram of the human following robot](assets/circuit-diagram.png)

---

## 📌 Pin connections

### L298N motor driver

| L298N pin | Arduino Uno pin | Purpose |
|---|---|---|
| ENA (left speed, PWM) | `5` | Controls left-side motor speed |
| IN1 (left direction) | `8` | Left motor direction bit A |
| IN2 (left direction) | `7` | Left motor direction bit B |
| ENB (right speed, PWM) | `6` | Controls right-side motor speed |
| IN3 (right direction) | `4` | Right motor direction bit A |
| IN4 (right direction) | `12` | Right motor direction bit B |
| +12V / VCC | Battery pack | Motor power |
| 5V out / GND | Arduino 5V + GND | Powers the Uno (if using L298N's regulator) |

### HC-SR04 ultrasonic sensor

| Sensor pin | Arduino Uno pin |
|---|---|
| VCC | `5V` |
| GND | `GND` |
| Trig | `A1` |
| Echo | `A0` |

### IR sensors

| Sensor | Sensor pin | Arduino Uno pin |
|---|---|---|
| Left IR | OUT | `A2` |
| Left IR | VCC / GND | `5V` / `GND` |
| Right IR | OUT | `A3` |
| Right IR | VCC / GND | `5V` / `GND` |

### Servo

| Servo wire | Arduino Uno pin |
|---|---|
| Signal (orange/yellow) | `10` |
| VCC (red) | `5V` |
| GND (brown/black) | `GND` |

> ⚠️ If your build is wired differently, just update the `#define` pin numbers at the top of [`human_following_robot.ino`](human_following_robot.ino) to match.

---

## ⚙️ How it works

1. The **ultrasonic sensor** continuously measures the distance to whatever is directly ahead.
2. If that distance is within `FOLLOW_DISTANCE`, the robot considers a person "detected":
   - If only the **left IR sensor** is triggered → turn left.
   - If only the **right IR sensor** is triggered → turn right.
   - If neither/both are triggered → drive straight forward.
   - If the distance drops below `STOP_DISTANCE` → stop.
3. If nothing is detected for `SEARCH_DELAY` milliseconds, the robot stops and **sweeps the servo** left and right, checking distance at each side to try to reacquire the person.

```
Ultrasonic sees person? ── No ──► Stop + sweep servo to search
        │ Yes
        ▼
Too close? ── Yes ──► Stop
        │ No
        ▼
Left IR only? ── Yes ──► Turn left
        │ No
        ▼
Right IR only? ── Yes ──► Turn right
        │ No
        ▼
    Drive forward
```

---

## 🚀 Getting started

1. **Wire the robot** according to the [pin connections](#-pin-connections) table above.
2. Install the **Arduino IDE** ([download here](https://www.arduino.cc/en/software)) and make sure the built-in `Servo` library is available (it ships with the IDE by default).
3. Open [`human_following_robot.ino`](human_following_robot.ino) in the Arduino IDE.
4. Select **Tools → Board → Arduino Uno** and the correct **Port**.
5. Click **Upload**.
6. Open the **Serial Monitor** (9600 baud) to watch live distance and sensor readings while you test.

---

## 🎛️ Tuning parameters

All of these are plain variables near the top of the sketch — no need to touch the logic below them:

| Variable | Default | What it does |
|---|---|---|
| `STOP_DISTANCE` | `15` cm | How close the person can get before the robot halts |
| `FOLLOW_DISTANCE` | `40` cm | Max distance the robot still considers "following range" |
| `BASE_SPEED` | `160` (0–255) | Motor speed when driving straight forward |
| `TURN_SPEED` | `140` (0–255) | Motor speed while steering left/right |
| `SEARCH_DELAY` | `1500` ms | How long to wait with nothing detected before searching |
| `IR_ACTIVE_STATE` | `LOW` | Flip to `HIGH` if your IR sensors report backwards |

---

## 📁 Project structure

```
human-following-robot/
├── human_following_robot.ino   # Main Arduino sketch
├── assets/
│   └── circuit-diagram.png     # Wiring diagram
└── README.md
```

---

## 🔮 Possible improvements

- Replace the two IR sensors with a servo-mounted ultrasonic sweep for smoother, more precise angle tracking
- Add a PID controller for smoother speed/turning instead of fixed speeds
- Add a battery voltage monitor to prevent over-discharge
- Swap IR obstacle sensors for a proper IR/PIR human-presence sensor to reduce false positives

---

