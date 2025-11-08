<div align="center">

# AC Motor Phase Control Driver: RTL to PCB Fabrication

</div>

<div align="center">

![Electronics](https://img.shields.io/badge/Domain-Power%20Electronics-blue?style=for-the-badge)
![Technology](https://img.shields.io/badge/Tech-AC%20230V%2050Hz-green?style=for-the-badge)
![Microcontroller](https://img.shields.io/badge/MCU-Arduino%20Nano%20Every-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Fabrication%20Ready-success?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

**A complete design of an isolated AC Motor Speed Controller based on phase-angle TRIAC firing control.**

[Overview](#-overview) • [Architecture](#-system-architecture) • [Design Flow](#-complete-design-flow) • [Calculations](#-critical-circuit-calculations) • [PCB](#-pcb-layout--manufacturing) • [Assembly](#-assembly--testing) • [Code](#-firmware) • [Safety](#-safety-guidelines)

---

</div>

## 🎯 Overview

This project implements a **230V AC motor speed controller** using **phase-angle control**.  
A **TRIAC (BT136-500)** regulates the motor’s power by adjusting its conduction angle within each AC cycle, while a **MOC3021M optotriac** ensures **galvanic isolation** between high-voltage and low-voltage domains.

The system is controlled by an **Arduino Nano Every**, reading a potentiometer input and generating firing pulses accordingly.

### Key Features

| Feature | Benefit |
|---|---|
| **Phase-Angle AC Power Control** | Smooth motor speed variation |
| **Complete Isolation via MOC3021M** | Protects MCU from mains voltage |
| **BT136-500 TRIAC** | Supports loads up to **300W** safely |
| **Professional 2-Layer PCB** | Fabrication-ready with DRC/LVS clean checks |
| **Safe Input & Output Terminaling** | Field-ready connection method |
| **Fully Documented Calculations** | Engineering-validated design approach |

---

## 🏗 System Architecture

### Block Diagram

┌──────────────────────┐ ┌──────────────────────┐
│ Arduino Nano Every │ Firing Pulse │ MOC3021M Opto │
│ (Low Voltage Domain) ├───────────────▶│ Isolated Triac Gate │
└──────────┬───────────┘ └──────────┬───────────┘
│ │
Pot Input Gate Drive
│ │
▼ ▼
Analog Control ┌──────────────┐
│ BT136-500 │
│ TRIAC │
└─────┬────────┘
│
AC Output to Motor
│
AC Mains Input ─────────┘

yaml
Copy code

### Operating Principle

| Step | Action |
|---|---|
| 1 | Potentiometer sets speed reference |
| 2 | MCU converts analog value → firing delay |
| 3 | MCU outputs gate pulse delayed from AC zero crossing |
| 4 | MOC3021 triggers BT136 gate safely |
| 5 | BT136 conducts for remainder of half-cycle → Controls power |

---

## 🔄 Complete Design Flow

Specification → Circuit Design → Calculation Validation → KiCad Schematic →
PCB Layout → DRC & ERC Checks → Gerber Generation → Fabrication → Assembly → Testing

yaml
Copy code

---

## 📐 Critical Circuit Calculations

### 1. Optocoupler LED Resistor (R1 = 200Ω)

Vout_Arduino = 5V
Vf_LED = 1.2V
R1 = (5 - 1.2) / 19mA = 200Ω

yaml
Copy code

✅ Operating safely above minimum trigger current.

---

### 2. TRIAC Gate Trigger Resistor (R6 = 180Ω)

For **230V AC**:

Vpeak = 230 × 1.414 = 325.3V
I_gate = Vpeak / R6 ≈ 1.8A (pulse)

yaml
Copy code

✅ Within **BT136 peak I<sub>G</sub> rating** (2A max).

---

### 3. PCB Power Trace Heating

Trace width: 1.0mm
Copper thickness: 35μm
Load: 1–2A

Temperature rise ≈ 10°C under continuous load

csharp
Copy code

✅ Safe for **300W motor use**.

---

## 🖥 Firmware

<details>
<summary><strong>📦 Click to View Arduino Code</strong></summary>

```cpp
#define POT_PIN A0
#define TRIAC_PIN 9

const float AC_FREQ = 50.0;
const float HALF_CYCLE_US = (1e6 / (2 * AC_FREQ));

const int MIN_DELAY_US = 500;
const int MAX_DELAY_US = 9000;

void setup() {
  pinMode(TRIAC_PIN, OUTPUT);
  digitalWrite(TRIAC_PIN, LOW);
}

void loop() {
  int potValue = analogRead(POT_PIN);
  int firingDelay = map(potValue, 0, 1023, MAX_DELAY_US, MIN_DELAY_US);

  delayMicroseconds(firingDelay);

  digitalWrite(TRIAC_PIN, HIGH);
  delayMicroseconds(100);
  digitalWrite(TRIAC_PIN, LOW);

  delayMicroseconds(HALF_CYCLE_US - firingDelay);
}
</details>
🧱 PCB Layout & Manufacturing
Parameter	Value
Board Size	47mm × 51mm
Layers	2 (Top Signal + Bottom Ground Plane)
Min Trace/Space	0.2mm
High-Voltage Clearance	≥ 3.0mm
Finish	HASL
Copper	1 oz

<div align="center">
(Insert your PCB images here — recommended filenames)

images/layout_top.png
images/layout_bottom.png
images/3d_view.png

</div>
🛠 Assembly & Testing
Tools Required
Soldering iron (350°C)

SMD tweezers

Multimeter

Isolation transformer (strongly recommended)

Before Powering AC:
sql
Copy code
✅ Verify no short between control & AC ground
✅ Ensure TRIAC orientation matches silkscreen
✅ Check terminal block tightening
⚠ Safety Guidelines
vbnet
Copy code
⚡ This circuit is directly connected to 230V AC mains.
Mistakes can cause shock, fire, or fatal injury.
Mandatory Rules:

Do not touch PCB while powered.

Do not power Arduino via USB when AC is connected.

Always test using an isolation transformer.

Enclose PCB in insulated casing before real use.

📬 Contact
Pranjal Upadhyay
Department of Electronics & Communication Engineering
IIITDM Kurnool

📧 Email: pranjal2004upadhyay@gmail.com
🔗 GitHub: https://github.com/upadhyaypranjal
🔗 LinkedIn: https://www.linkedin.com/in/pranjalupadhyay0142

📝 License
MIT License — free to use, modify, distribute.

<div align="center">
⭐ If this project helped you — consider starring the repository!
</div> ```
