<p align="center">
  <img src="./Images/Porus Lab.png" alt="Porus Lab Logo" width="300">
</p>

# Porus Lab Board-03: Generic Analog Signal Conditioning Interface Board

A modular analog signal conditioning board designed to interface analog sensor outputs with ADC-based control platforms such as **TI C2000™, STM32™, Microcontrollers, dSPACE®, OPAL-RT®, FPGA**, and other data acquisition systems.

Although developed as the companion board for **Porus Lab Board-02 (Voltage & Current Sensor PCB)**, this board is designed as a **generic analog interface** and can be used with virtually any analog sensor that provides a voltage output.

---

# Overview

Many analog sensors cannot be connected directly to an ADC because the signals often require:

- High input impedance buffering
- Noise filtering
- Signal attenuation or amplification
- Level shifting
- ADC input protection

Instead of integrating these functions into every sensor PCB, the signal conditioning circuitry is implemented as a dedicated interface board.

This modular architecture provides greater flexibility and allows the same sensor board to be reused with different controllers by simply changing the interface configuration.

---

# Features

- High input impedance voltage buffer
- Configurable active low-pass filter
- Adjustable gain (attenuation/amplification)
- LM317-based adjustable reference voltage generation
- Jumper-selectable level shifting
- Configurable unidirectional or bidirectional operation
- Output protection against negative voltage and over-voltage
- Standardized 12-pin IDC connector
- Modular architecture
- Compatible with multiple dual operational amplifiers

---

# Supported Platforms

- TI C2000
- STM32
- Arduino
- Raspberry Pi ADC
- dSPACE
- OPAL-RT
- FPGA
- Any ADC-based controller

---

# Supported Sensors

Since this board conditions **analog voltage signals**, it can be used with a wide variety of sensors including:

- Voltage Sensors
- Current Sensors
- Pressure Sensors
- Temperature Sensors
- Hall Sensors
- Position Sensors
- LVDTs
- Potentiometric Sensors
- Analog Transducers

Although primarily designed for **Porus Lab Board-02**, the interface board is **not limited to LEM sensors**.

---

# System Architecture

<p align="center">
<img src="./Images/Sensor and interface board.png" width="900">
</p>

**Figure 1. Overall Modular Architecture**

The sensing circuit and the signal conditioning circuit are intentionally separated.

This provides:

- reusable sensing hardware
- controller-independent signal conditioning
- easier maintenance
- customizable ADC interfaces
- improved scalability

The overall signal flow is

```
Sensor
   │
   ▼
Voltage Buffer
   │
   ▼
Low-Pass Filter
   │
   ▼
Level Shifter
   │
   ▼
Protection Circuit
   │
   ▼
ADC
```

---

# Circuit Description

<p align="center">
<img src="./Images/Circuit Diagram.png" width="900">
</p>

**Figure 2. Complete Signal Conditioning Circuit**

The interface board consists of five functional blocks:

1. Voltage Buffer
2. Active Low-Pass Filter
3. Adjustable Reference Voltage Generator
4. Level Shifter
5. Output Protection

---

# 1. Voltage Buffer

The first stage is a unity-gain voltage follower.

Its purpose is to

- provide high input impedance
- prevent loading of the sensor
- provide low output impedance
- improve signal integrity

This allows the remaining signal conditioning stages to operate independently of the sensor characteristics.

---

# 2. Active Low-Pass Filter

<p align="center">
<img src="./Images/Filter Circuit.png" width="420">
</p>

**Figure 3. Active Low-Pass Filter**

The active filter attenuates switching ripple and high-frequency noise commonly present in power electronic converters.

The DC gain is

\[
G=\frac{V_2}{V_1}=-\frac{R_f}{R_1}
\]

Choose

- **Rf**
- **R1**

according to the required gain.

The cutoff frequency is determined by

**Cf**

making the circuit easily adaptable to different sensing applications.

---

# 3. Adjustable Reference Voltage Generator

<p align="center">
<img src="./Images/Reference Voltage Generaton Circuit.PNG" width="550">
</p>

**Figure 4. LM317 Adjustable Reference Voltage Generator**

The interface board includes an adjustable reference voltage source based on the **LM317** voltage regulator.

The generated reference voltage is primarily used by the level-shifting circuit to translate bipolar signals into the operating range of unipolar ADCs.

### Features

- Adjustable output voltage
- Approximately **1.2 V to 15 V**
- Stable low-noise reference
- Test point for calibration
- Jumper-selectable routing

The output voltage is adjusted using the onboard potentiometer.

Typical reference voltages are

| Controller | Reference Voltage |
|------------|-------------------|
| TI C2000 | 1.5 V |
| STM32 | 1.65 V |
| Arduino | 2.5 V |
| ESP32 | 1.65 V |

---

# 4. Level Shifter

<p align="center">
<img src="./Images/Level Shifter.png" width="420">
</p>

**Figure 5. Level Shifter**

Many controllers cannot measure bipolar voltages.

The level shifter offsets the signal according to

\[
V_o = V_{in}+V_{ref}
\]

The reference voltage is provided by the LM317 circuit.

If level shifting is not required,

**open LS-JMP**

and the stage behaves as a normal buffer.

---

# 5. Output Protection

<p align="center">
<img src="./Images/Protection Circuits.png" width="420">
</p>

**Figure 6. Output Protection Circuit**

The protection circuit prevents accidental damage to ADC inputs.

### Controller Mode

Suitable for

- TI C2000
- STM32
- Arduino

Configuration

- Keep **J1 Open**
- Keep **J2 Closed**
- Install suitable Zener diode

Example

3.0 V Zener

Output becomes

```
0 V

↓

3 V
```

---

### Bipolar Mode

Suitable for

- dSPACE
- OPAL-RT
- ±10 V ADC cards

Configuration

- Close J1
- Open J2

This bypasses the negative-voltage blocking diode and removes the positive voltage clamp, allowing bipolar signals to reach the controller.

---

# Operational Amplifier Selection

The PCB is designed with a **standard dual operational amplifier footprint**, allowing users to choose an operational amplifier based on performance and cost requirements.

The board has been verified with the following devices:

| Device | Advantages | Recommended Application |
|---------|------------|-------------------------|
| **LM358** | Low cost, widely available, suitable for general-purpose signal conditioning | Educational laboratories and cost-sensitive applications |
| **LM833** | High slew rate, low offset voltage, low noise, and improved bandwidth | High-performance signal conditioning for power electronics research and control applications |

The PCB footprint is compatible with both devices, allowing users to select the most suitable operational amplifier based on their application requirements.

---

# LTspice Simulation

<p align="center">
<img src="./Images/Simulation Diagram.jpg" width="900">
</p>

**Figure 7. LTspice Verification**

Simulation files are included to verify

- Filter response
- Level shifting
- Signal integrity
- Output protection

---

# PCB Files

This repository includes

- Schematics
- PCB Layout
- Gerber Files
- NC Drill Files
- Bill of Materials (BOM)
- LTspice Simulation Files

---

# Integration with Porus Lab Board-02

This board interfaces directly with

**Porus Lab Board-02: Voltage & Current Sensor PCB**

through the standard **12-pin IDC connector**.

The sensor board performs isolated voltage/current measurement, while this interface board prepares the analog signals for acquisition by

- TI C2000
- STM32
- dSPACE
- OPAL-RT
- FPGA
- Other ADC-based systems

## Componenets Details

|Name                   |Value              |Footprint              |
|-----------------------|-------------------|-----------------------|
|CD                     | 0.1uF             |1206 SMD               |
|Input IDC socket       | 12 pin            |2.54 mm pp             |
|Op-amps (LM358/833)    |                   |SOIC-8                 |
|Resitors               |  Based on Cal.    |1206 SMD               |

The voltage reference circuit values mentioned in below fig:

<img src="./Images/Voltage Ref Circuit.png" width="300">


---


This project is released for educational and research purposes.

**Srinivasarao (Porus) Kavuru**