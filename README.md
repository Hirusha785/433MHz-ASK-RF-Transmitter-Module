# 📡 433MHz ASK RF Transmitter Module

![Status](https://img.shields.io/badge/status-open--hardware-blue)
![Frequency](https://img.shields.io/badge/frequency-433MHz-green)
![Modulation](https://img.shields.io/badge/modulation-ASK-orange)
![Hardware](https://img.shields.io/badge/hardware-PCB-purple)
![Platform](https://img.shields.io/badge/platform-Arduino%20%7C%20ESP32-blueviolet)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

## 🚀 Overview

**433MHz ASK RF Transmitter Module** is a compact open-hardware wireless transmitter PCB designed for sending **433MHz Amplitude Shift Keying signals** from microcontrollers and embedded systems.

This project includes a custom schematic, PCB layout, RF coil antenna section, transistor-based RF stage, LC tuning network, and simple 3-pin interface for easy integration with Arduino, ESP32, STM32, Raspberry Pi Pico, and other embedded platforms.

The module is suitable for RF learning, short-range wireless communication, remote-control experiments, sensor transmitters, IoT nodes, and custom embedded hardware projects.

---

## ✨ Key Features

- 📡 **433MHz RF transmission**
- 📶 **ASK modulation**
- 🔌 **Simple 3-pin interface: VCC, DATA, GND**
- ⚡ **Transistor-based RF driver stage**
- 🧲 **LC tuning network**
- 🔁 **Microcontroller-controlled data transmission**
- 📻 **Helical antenna support**
- 🧩 **Compact PCB layout**
- 🛠️ **Open-hardware design**
- 🧪 **Useful for RF testing, learning, and prototyping**

---

## 🧠 Technical Description

This module is designed to transmit digital data using **Amplitude Shift Keying** at approximately **433MHz**.

A microcontroller sends a digital signal to the **DATA** pin. This signal controls the transmitter stage and modulates the RF carrier amplitude. The RF signal is generated and shaped using transistor stages, coils, capacitors, and a frequency reference section.

The output is transmitted through the antenna section, which can use a helical antenna or a properly sized wire antenna depending on range and space requirements.

---

## 🧱 System Block Diagram

```text
Microcontroller GPIO
        ↓
DATA Input
        ↓
ASK Modulation Stage
        ↓
RF Oscillator / Frequency Reference
        ↓
Transistor RF Driver Stage
        ↓
LC Tuning Network
        ↓
Antenna Output
        ↓
433MHz Wireless Transmission
```

---

## 🔌 Pinout

| Pin | Name | Description |
|---|---|---|
| 1 | VCC | Power input |
| 2 | DATA | ASK data input from microcontroller |
| 3 | GND | Ground connection |

---

## 🧩 Main Components

| Component | Value / Type | Function |
|---|---|---|
| AE1 | Antenna | Transmits the 433MHz RF signal |
| L1 | 8 turns | RF tuning / oscillator coil |
| L2 | 3 turns | Antenna coupling / RF output coil |
| C1 | 7pF | RF tuning capacitor |
| C2 | 7pF | Antenna coupling capacitor |
| R1 | 27kΩ | Biasing resistor |
| R2 | 27kΩ | DATA input / modulation resistor |
| Q1 | 2SC4213 | RF transistor stage |
| Q2 | 2SC4213 | Modulation / RF driver transistor stage |
| Y1 | Crystal / Resonator | Frequency reference component |
| J1 | 3-pin connector | VCC, DATA, GND interface |

---

## ⚙️ Hardware Design Sections

### 1. Power Input Section

The module receives power through the **VCC** and **GND** pins.

A stable power supply is important because RF output strength and frequency stability can be affected by voltage noise or weak supply current.

---

### 2. DATA Input Section

The **DATA** pin receives a digital signal from a microcontroller.

Typical connection:

```text
Microcontroller VCC   →  VCC
Microcontroller GPIO  →  DATA
Microcontroller GND   →  GND
```

The DATA input controls the ASK modulation behavior of the transmitter.

---

### 3. RF Oscillator Section

The oscillator section generates the RF carrier signal around the 433MHz band.

The frequency reference component, transistor stage, coils, and capacitors help form the RF signal generation network.

---

### 4. ASK Modulation Section

ASK means **Amplitude Shift Keying**.

In this module, the input data changes the amplitude of the RF signal.  
When the DATA signal changes between HIGH and LOW states, the RF output amplitude changes accordingly.

---

### 5. RF Driver Stage

The transistor-based RF stage helps drive the RF signal toward the antenna section.

This stage is important for signal strength and transmission range.

---

### 6. Antenna Output Section

The antenna section radiates the 433MHz RF signal.

A properly designed antenna improves:

- Transmission distance
- Signal stability
- RF efficiency
- Receiver detection quality

---

## 🧪 Testing Methods

You can test this transmitter module using:

- Arduino
- ESP32
- STM32
- Raspberry Pi Pico
- Oscilloscope
- SDR receiver
- 433MHz ASK receiver module
- Logic analyzer
- RF spectrum analyzer

Example testing flow:

```text
Microcontroller
      ↓
433MHz ASK Transmitter Module
      ↓
Wireless RF Signal
      ↓
433MHz ASK Receiver Module
      ↓
Microcontroller / Logic Analyzer
```

---

## 🧑‍💻 Arduino Basic ASK Transmit Code

```cpp
#define TX_DATA_PIN 2

const unsigned int BIT_DELAY_US = 500;

void sendBit(bool bitValue) {
  digitalWrite(TX_DATA_PIN, bitValue ? HIGH : LOW);
  delayMicroseconds(BIT_DELAY_US);
}

void sendByte(byte data) {
  for (int i = 7; i >= 0; i--) {
    sendBit((data >> i) & 0x01);
  }
}

void sendFrame(byte payload) {
  // Preamble for receiver synchronization
  for (int i = 0; i < 8; i++) {
    sendByte(0xAA);
  }

  // Sync bytes
  sendByte(0x2D);
  sendByte(0xD4);

  // Payload
  sendByte(payload);

  // Simple inverted payload for basic checking
  sendByte(~payload);

  // Stop transmission
  digitalWrite(TX_DATA_PIN, LOW);
}

void setup() {
  pinMode(TX_DATA_PIN, OUTPUT);
  digitalWrite(TX_DATA_PIN, LOW);
}

void loop() {
  static byte counter = 0;

  sendFrame(counter);
  counter++;

  delay(1000);
}
```

---

## 🧑‍💻 ESP32 Basic ASK Transmit Code

```cpp
#define TX_DATA_PIN 27

const unsigned int BIT_DELAY_US = 500;

void sendBit(bool bitValue) {
  digitalWrite(TX_DATA_PIN, bitValue ? HIGH : LOW);
  delayMicroseconds(BIT_DELAY_US);
}

void sendByte(uint8_t data) {
  for (int i = 7; i >= 0; i--) {
    sendBit((data >> i) & 0x01);
  }
}

void sendFrame(uint8_t payload) {
  // Preamble for receiver synchronization
  for (int i = 0; i < 8; i++) {
    sendByte(0xAA);
  }

  // Sync bytes
  sendByte(0x2D);
  sendByte(0xD4);

  // Payload
  sendByte(payload);

  // Simple inverted payload for basic checking
  sendByte(~payload);

  // Stop transmission
  digitalWrite(TX_DATA_PIN, LOW);
}

void setup() {
  pinMode(TX_DATA_PIN, OUTPUT);
  digitalWrite(TX_DATA_PIN, LOW);
}

void loop() {
  static uint8_t counter = 0;

  sendFrame(counter);
  counter++;

  delay(1000);
}
```

---

## 🔬 Oscilloscope / RF Testing

Recommended testing points:

| Test Point | Purpose |
|---|---|
| DATA pin | Check microcontroller input waveform |
| Q2 stage | Check modulation switching behavior |
| Oscillator section | Verify RF activity |
| Antenna output | Check transmitted RF signal |
| Receiver output | Confirm wireless transmission |

---

## 🖼️ Project Preview

### Schematic

<img width="1193" height="644" alt="image" src="https://github.com/user-attachments/assets/0f902380-955a-49c1-8a7a-7ff1aa47f053" />

![Schematic](images/schematic.png)

### PCB Layout

<img width="775" height="806" alt="image" src="https://github.com/user-attachments/assets/402621e0-caf0-41ac-b119-4b250f32bf47" />

![PCB Layout](images/pcb-layout.png)

### 3D View

<img width="995" height="846" alt="image" src="https://github.com/user-attachments/assets/8c0c279e-b197-48bf-9ce6-774926d3552f" />
<img width="822" height="778" alt="image" src="https://github.com/user-attachments/assets/7da37d87-4f92-4169-8e45-9208676b239a" />
<img width="860" height="670" alt="image" src="https://github.com/user-attachments/assets/b74d1afb-f79f-4ded-a1f1-49a84a6c1bef" />
<img width="915" height="745" alt="image" src="https://github.com/user-attachments/assets/30755326-ef17-42c2-9848-2faa3fe69566" />

![3D View](images/3d-front.png)

---

## 🛠️ PCB Design Notes

- Keep the antenna trace short and clean.
- Keep RF traces away from noisy digital signals.
- Use a stable ground reference around the RF section.
- Place RF tuning components close to the transistor RF stage.
- Avoid unnecessary long traces in the oscillator section.
- Keep the DATA input trace away from the antenna path where possible.
- Use short routing between the LC network and antenna output.
- Avoid sharp corners in RF traces.
- Test the transmitter with a receiver or SDR before final production.
- RF performance depends heavily on antenna design, PCB layout, supply voltage, and component tolerance.

---

## 📡 Antenna Notes

For 433MHz, a common quarter-wave wire antenna length is approximately:

```text
17.3 cm
```

A helical antenna can also be used for compact designs.

The final range depends on:

- Antenna type
- Antenna tuning
- Supply voltage
- PCB layout
- Receiver sensitivity
- Environmental interference
- Distance and obstacles

---

## ⚠️ Important Notes

This project is intended for:

- Educational use
- RF learning
- Embedded hardware experiments
- Short-range wireless prototyping
- ASK transmitter testing

RF transmission is regulated in many countries.  
Before commercial or public deployment, make sure the design follows local RF regulations, frequency limits, duty-cycle limits, and output power limits.

---

## 👨‍💻 Author

Designed by **Fernando S.M.H.G**

Open-hardware RF transmitter project for embedded systems, electronics learning, RF experimentation, and wireless communication projects.

---

## 📜 License

This project is released under the **MIT License**.

You are free to use, modify, improve, and share this project with proper attribution.

---

## ⭐ Support

If this project helps you, consider giving the repository a star.

```text
Made for RF learning, embedded systems, and open-hardware development.
```
````
