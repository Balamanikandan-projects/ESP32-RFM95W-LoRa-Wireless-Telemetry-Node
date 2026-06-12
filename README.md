# Custom ESP32 & RFM95W LoRa Wireless Telemetry Node

[Full 3D board render]
<img width="1176" height="624" alt="Screenshot 2026-06-12 101453" src="https://github.com/user-attachments/assets/1bf3fd3e-4e89-46fb-873b-7da616eeaeb4" />
<img width="580" height="504" alt="Screenshot 2026-06-12 101537" src="https://github.com/user-attachments/assets/1a3816c0-b0f6-471a-bce3-141a51a3d606" />

A compact, high-performance, **4-layer custom PCB design** integrating an ESP32-WROOM-32E microcontroller with an RFM95W sub-GHz LoRa radio module. This hardware platform is engineered as a robust, low-power remote data acquisition node featuring an optimized power distribution network (PDN) and controlled-impedance RF path layout.

---

## 🚀 Key Features & Architectural Highlights

* **Core Processing:** Powered by an **ESP32-WROOM-32E**, managing application firmware, peripheral communication via SPI/UART, and deep-sleep low-power states.
* **Long-Range RF Connectivity:** Integrated **HOPERF RFM95W LoRa module** mapped over a high-speed synchronous SPI bus for long-range, sub-GHz wireless telemetry.
* **Advanced 4-Layer Stackup:** Engineered with a strict high-speed layout methodology:
  * **Layer 1 (Top):** Signal routing, RF traces, and critical high-frequency component placement.
  * **Layer 2 (Inner 1):** Solid **GND Plane** for low-impedance return paths and EMI shielding.
  * **Layer 3 (Inner 2):** Split **Power Plane** (+3.3V, VBAT, +5V) to ensure low-drop DC distribution.
  * **Layer 4 (Bottom):** Slow-moving control signals, programming buses, and local ground copper fill.
* **Dual-Input Automated Power Path:** Seamless automated power switching between USB Type-C ($5\text{V}$) and a Lithium-Polymer battery ($3.7\text{V} - 4.2\text{V}$) using a low-forward-drop Schottky barrier isolation diode.
* **Integrated Battery Management:** Onboard **TP4056 linear LiPo battery charger** with a programmable constant-current resistor network to regulate charging states safely.
* **High-Transient Power Regulation:** Equipped with a **TLV62569 synchronous buck regulator** providing a rock-solid $+3.3\text{V}$ rail capable of delivering up to $2\text{A}$ to handle active RF and Wi-Fi transmission bursts without voltage sags.

![PCB Layer Stackup Configuration]
<img width="1066" height="547" alt="Screenshot 2026-06-12 100911" src="https://github.com/user-attachments/assets/a2858f8d-2376-49bb-b635-14b87f8fbcfb" />
<img width="1069" height="548" alt="Screenshot 2026-06-12 101321" src="https://github.com/user-attachments/assets/9995b5cb-dab9-4237-887e-b26e4d47a959" />
<img width="1068" height="547" alt="Screenshot 2026-06-12 101311" src="https://github.com/user-attachments/assets/352bbf31-16ef-4c5f-8017-2396a53f4dbc" />

---

## 🛠️ Hardware System Architecture & Floorplanning

The physical PCB layout is meticulously split into isolated functional blocks (Islands) to maximize signal integrity and prevent noise coupling:

### 1. Power Input & Charging (Zone 1)
* **USB-C Interface:** Standard 16-pin USB-C receptacle configured with dedicated $5.1\text{k}\Omega$ pull-down resistors on CC1/CC2 lines for downstream power negotiation.
* **Input Filtering:** High-frequency ceramic decoupling capacitors ($10\mu\text{F}$, 0603) placed immediately adjacent to the VBUS pins to suppress transient input ripples.

### 2. DC-DC High-Frequency Switching Regulator (Zone 2)
* **Switching Loop Minimization:** The $2.2\mu\text{H}$ power inductor ($L_1$) and input/output filters are placed directly next to the TLV62569 pads to minimize the high $di/dt$ switching loop area.
* **Isolated Feedback Network:** The voltage divider feedback resistors ($R_4, R_5$) are trace-isolated away from the noisy switching node to prevent output voltage fluctuations.

### 3. Microcontroller & Boot Strapping Constraints (Zone 3)
* **Deterministic Boot Logic:** Hardware pull-up and pull-down networks ($10\text{k}\Omega$) are hardwired on critical strapping pins (`GPIO 0`, `GPIO 5`, `GPIO 15`) to force stable system execution modes during reset and eliminate ambient floating noise.
* **Antenna Edge Clearance:** The ESP32's onboard trace antenna is positioned over a complete physical board overhang keepout zone, keeping all 4 copper layers fully clear of planes and traces to ensure 100% radiation efficiency.

### 4. RF Interface & Impedance Matching (Zone 4)
* **Bus Topography:** Parallel high-speed SPI bus routing (`MOSI`, `MISO`, `SCK`, `NSS`) designed with uniform lengths.
* **$50\Omega$ Antenna Path:** A straight-line **Pi-matching network** layout (Shunt C / Series R / Shunt C) routed into an edge-mounted coaxial SMA antenna jack, structured as a controlled $50\Omega$ microstrip transmission line.

### 5. UART Programming Breakout (Zone 5)
* Exposes standard `GND`, `TXD0`, `RXD0`, and `+3.3V` connections via a 4-pin male header for secure interface access using standard external USB-to-UART bridging modules.
![Complete KiCad Schematic Capture]
<img width="1362" height="762" alt="Screenshot 2026-06-12 100724" src="https://github.com/user-attachments/assets/4ed57f4c-6ed4-4fef-9238-83c75a09fe0a" />
<img width="1365" height="767" alt="Screenshot 2026-06-12 100810" src="https://github.com/user-attachments/assets/eaa622c5-cbc4-4d08-9b4d-f559b48b6c49" />
<img width="1365" height="767" alt="Screenshot 2026-06-12 100751" src="https://github.com/user-attachments/assets/a21004e1-855e-4dde-b834-8edfe974e6aa" />
<img width="1365" height="767" alt="Screenshot 2026-06-12 100732" src="https://github.com/user-attachments/assets/55ad853d-adca-482f-9bb8-98aade38ac60" />

---

## 📊 Verification, Design Rules & Layout Integrity

* **Electrical Rules Check (ERC):** Achieved a clean **0 Errors / 0 Warnings** baseline in KiCad, eliminating overlapping net labels, unconfigured power pins, or short circuits.
* **Design Rules Check (DRC):** Clean zero-error verification. Surface-mount pad routing challenges (such as USB-C tight mechanical clearances) were bypassed using direct-to-plane localized micro-vias down to the Inner Layer 2 Ground plane, reducing track parasitics and eliminating top-layer signal crowding.
![DRC Clean Pass Window]
<img width="1071" height="574" alt="Screenshot 2026-06-12 104146" src="https://github.com/user-attachments/assets/5f09216a-3f69-43e5-97ee-ea7dc040d9f1" />


---

## 💻 Technical Toolstack
* **EDA Suite:** KiCad (Schematic Capture, Custom Footprint Management, 4-Layer PCB Layout)
* **Component Form Factor:** Optimized using a space-saving $0603$ SMD footprint strategy for passives to secure layout compactness while maintaining manual bench-test and debugging solderability.
