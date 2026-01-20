# Medication Routine Awareness Device

### Project Overview
The Medication Routine Awareness Device is an IoT-enabled system that tracks medication intake using weight based sensing to provide real time physical feedback. By monitoring the weight of a pill container and displaying adherence status on a physical gauge, it helps users avoid missed or delayed doses through a simple, glanceable interface.

![System Overview](images/system-sketch.png)

---

### Sensing Device (The "Sensor" Slide)
The Sensing Device is a low-profile base that monitors the presence and weight of a medication bottle. It uses a high-precision **TAL220 Straight Bar Load Cell** coupled with an **HX711 24-bit ADC Amplifier** to detect when a single pill has been removed. The system is controlled by an **XIAO ESP32C3** microcontroller and powered by a **3.7V LiPo Battery (LP 653042)**. When a weight change is detected, the device processes the data and transmits a status update via Bluetooth Low Energy (BLE).



---

### Physical Display Device (The "Display" Slide)
The Display Device provides visual feedback on the user's medication status. It features a physical gauge needle driven by a **28BYJ-48 Stepper Motor** (controlled via a **ULN2003 Driver**). The needle points to "Normal," "Delayed," or "Missed" based on the time elapsed since the last recorded intake. It includes a **WS2812B RGB LED** for ambient status lighting and a tactile button for manual resets. An **SSD1306 OLED Screen** is used to show supplemental data like battery levels and precise timestamps.



---

### Communication & Functional Logic

#### 1. Communication Figure
The devices utilize a **BLE (Bluetooth Low Energy) Client-Server** architecture. The Sensing Device acts as the **Server**, notifying the Display Device (**Client**) whenever a valid "intake event" (weight drop) occurs.

`[Sensing Device (ESP32)] --(BLE GATT Notification)--> [Display Device (ESP32)]`

#### 2. Detailed Functional Diagram
The system logic follows a threshold-based timing pattern to determine the state of the display.



* **Logic Flow:**
    1.  **Sense:** Load cell detects weight change > 0.5g.
    2.  **Verify:** ESP32 filters noise and confirms bottle was replaced.
    3.  **Transmit:** BLE packet sent to Display Device.
    4.  **Process:** Display Device resets the "Dose Timer."
    5.  **Actuate:** Stepper motor moves needle back to "Normal" and LED turns green.
    6.  **Decay:** If timer exceeds 24 hours, motor moves needle to "Delayed."

## The Sensing Device (Hardware & Operation)
![Sensing Device Sketch](images/sensing_device.png)


The Sensing Device is a compact, battery-powered base designed to sit discreetly under a medication bottle. Its primary function is to detect "intake events" by monitoring precise weight fluctuations.

### How it Works:
1. **Weight Detection:** The device utilizes a **TAL220 Straight Bar Load Cell** mounted in a cantilever "sandwich" configuration. When the bottle is placed on the top plate, it exerts force on the load cell.
2. **Signal Amplification:** The minute electrical changes from the load cell are processed by an **HX711 24-bit ADC**, which converts the analog force into a digital value the microcontroller can understand.
3. **Data Processing:** A **Seeed Studio XIAO ESP32C3** filters the raw data to ignore vibrations or accidental bumps. It confirms a "dose taken" only when a specific weight (e.g., the weight of one pill) is removed and the bottle is replaced.
4. **Wireless Transmission:** Once confirmed, the XIAO uses its integrated antenna to send a **Bluetooth Low Energy (BLE)** trigger to the Display Device.
5. **Power Management:** The unit is powered by a **LP 653042 3.7V LiPo Battery**, which is recharged directly through the XIAO's onboard charging pads.

### Key Components:
* **Microcontroller:** Seeed Studio XIAO ESP32C3
* **Weight Sensor:** TAL220 Load Cell
* **Amplifier:** HX711 24-Bit Analog-to-Digital Converter
* **Power:** 1100mAh LiPo Battery (LP 653042)
