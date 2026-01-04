# Zero-Component True Random Number Generator (TRNG) via Dual-MCU Drift

![License](https://img.shields.io/badge/license-MIT-blue.svg) ![Platform](https://img.shields.io/badge/platform-ESP32-green.svg) ![Status](https://img.shields.io/badge/status-stable-brightgreen.svg)

## 📖 Overview

This project implements a **True Random Number Generator (TRNG)** using nothing but two standard ESP32 microcontrollers and a single jumper wire. 

Unlike pseudo-random number generators (PRNGs) like `random()` which rely on deterministic mathematical formulas, this system harvests **physical entropy** from the real world. It exploits the **Clock Drift** and **Thermal Jitter** between two independent silicon oscillators to generate cryptographically secure random numbers.

### Key Features
* **Zero External Components:** No resistors, capacitors, or avalanche diodes required.
* **Physical Entropy Source:** Relies on Heisenberg uncertainty in electron timing and thermal fluctuations.
* **Von Neumann Whitening:** Implements algorithmic post-processing to remove hardware bias.
* **High-Speed Visualization:** Includes a Processing-based dashboard to visualize entropy as White Noise in real-time.

---

## 🛠️ Hardware Setup

You need two ESP32 development boards.

1.  **Sender Board:** Generates the chaotic high-frequency signal.
2.  **Receiver Board:** Harvests the signal, processes the entropy, and sends data to the PC.

### Wiring Diagram

| Sender ESP32 (Board 1) | Connection | Receiver ESP32 (Board 2) |
| :--- | :---: | :--- |
| **GND** | ─── 🖤 ─── | **GND** |
| **GPIO 23** | ─── 🟢 ─── | **GPIO 26** |

> **⚠️ Safety Note:** Both boards operate at 3.3V logic. This direct connection is safe and will not damage your microcontrollers.

---

## 🚀 How It Works

### 1. The Chaos Source (Sender)
The Sender ESP32 is programmed to output a square wave at a **Prime Frequency (~4.13 MHz)**. Simultaneously, it runs a computationally intensive "Heater Loop" (complex floating-point math) to induce **Thermal Stress**. This heat causes the crystal oscillator to physically drift, preventing harmonic locking with the receiver.

### 2. The Sampling (Receiver)
The Receiver ESP32 samples the incoming signal. It uses a **Dynamic Jitter Delay** (waiting random microseconds between reads) to ensure it never synchronizes with the Sender.

### 3. The Whitening (Bias Removal)
Raw hardware signals often have a bias (e.g., more 1s than 0s). We use the **Von Neumann Whitening Algorithm** to extract pure randomness:
* Read bits in pairs `(a, b)`.
* If `a == b` (00 or 11): **Discard**.
* If `a != b`: **Keep the first bit**.
This ensures that even a biased source produces a uniform output distribution.

---

## 💻 Installation & Usage

### Step 1: Flash the Sender
1.  Open `Sender_Code.ino` in Arduino IDE.
2.  Select the port for **Board 1**.
3.  Upload.
4.  *Optional:* You can power this board via USB or a battery bank; it does not need to be connected to the PC for data.

### Step 2: Flash the Receiver
1.  Open `Receiver_Code.ino` in Arduino IDE.
2.  Select the port for **Board 2** (e.g., `COM4`).
3.  **Important:** Upload speed is standard, but the code uses `Serial.begin(230400)`.
4.  Upload.

### Step 3: Run the Visualizer
1.  Download [Processing](https://processing.org/download).
2.  Open `Visualizer.pde`.
3.  **Close Arduino Serial Monitor** (to free up the COM port).
4.  Edit the code to match your port: `String portName = "COM4";`.
5.  Click the **Play** button.

---

## 📊 Results

### Entropy Visualization (White Noise)
If the generator is working correctly, the visualizer will display a "TV Static" pattern. This indicates **High Entropy** (no repeating patterns, stripes, or waves).

<img width="937" height="672" alt="Screenshot 2026-01-03 233246" src="https://github.com/user-attachments/assets/28e109a1-423d-44c5-93ff-73219cd5ef8a" />


### Sample Output (Serial Terminal)
```text
Random: 142 | #############
Random: 6   | #
Random: 211 | ###################
Random: 89  | ########
