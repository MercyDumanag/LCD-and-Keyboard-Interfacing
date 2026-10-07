# LCD and Keypad Interfacing with STM32F407

A bare-metal firmware project implementing a 4-bit parallel 16x2 LCD driver and a GPIO matrix scanning algorithm for a 4x4 keypad using an **STM32F407** microcontroller.

---

## Overview

This project focuses on designing and implementing an embedded system to interface a 16x2 character LCD display and a 4x4 matrix keypad with the **STM32F407ZGT6-based RT-Spark Development Board**. 

### Key Objectives
* Understand the multi-mode operation of HD44780-compatible 16x2 LCD displays.
* Implement custom 4-bit parallel LCD display driver functions in Embedded C.
* Develop a active-low GPIO matrix scanning routine to read multi-button matrix keypad inputs.
* Gain hands-on experience in **STM32CubeIDE** using the STM32 HAL framework, GPIO configuration, and polling-based hardware debouncing.

---

## Hardware Specifications & Setup

The system is powered via USB connected to the host PC and utilizes the following hardware components:

* **Development Board:** RT-Thread RT-Spark Board (STM32F407ZGT6, ARM Cortex-M4)
* **Display:** 16x2 Character LCD (4-bit parallel interface)
* **Input Device:** 4x4 Matrix Keypad (16 keys: `0–9`, `A–F`, `*`, `#`)

### Pin Configuration & Wiring

| Module | Pin / Signal | STM32 GPIO Role | Configuration Notes |
| :--- | :--- | :--- | :--- |
| **LCD** | `D4` – `D7` | GPIO Outputs | 4-bit data bus lines |
| **LCD** | `RS` (Register Select) | GPIO Output | Command vs. Data mode |
| **LCD** | `EN` (Enable) | GPIO Output | Clock pulse signal |
| **LCD** | `RW` (Read/Write) | Grounded (`GND`) | Fixed Write-only mode |
| **Keypad** | Rows 1–4 | GPIO Outputs | Active-low output scanning |
| **Keypad** | Columns 1–4 | GPIO Inputs | Configured with internal pull-up resistors |

---

## Software Architecture & Implementation

The firmware was authored in **Embedded C/C++** inside **STM32CubeIDE** using the STM32 HAL library.

### 1. 16x2 LCD Driver (4-Bit Mode)
* **Initialization:** Sends specific 4-bit command nibbles to switch the display out of default 8-bit mode into 4-bit mode upon startup.
* **Nibble Transmission:** High-order and low-order nibbles are split and clocked sequentially using the Enable (`EN`) line.
* **API Functions:** Includes low-level methods for initialization, command execution (`lcd_send_cmd`), cursor positioning (`lcd_put_cur`), and string output (`lcd_send_string`).

### 2. Keypad Matrix Scanning Routine
* **Row-Column Scanning:** The MCU sequentially pulls one row pin **LOW** (0V) while keeping all other rows **HIGH**.
* **Column Reading:** Inputs on column pins are read continuously. When a key is pressed, it bridges the active row to that column, pulling the column pin **LOW**.
* **Debouncing & Decoding:** Microsecond software delays stabilize mechanical contact bounce. Captured decimal key values (10–15) are dynamically mapped and converted to hex ASCII characters (`A`–`F`). Error handling catches out-of-bounds input requests.

---

## Testing & Verification

Project validation was conducted across 6 structured activities:

1. **LCD Display Verification:** Initialized the LCD and verified basic text output by displaying centered user credentials and current year strings across Line 1 and Line 2.
2. **Keypad Individual Verification:** Tested all individual inputs—including numbers (`0–9`), letters (`A–F`), and symbols (`*`, `#`)—ensuring correct key press capture.
3. **Real-time Echo Test:** Validated real-time input-to-display latency using active polling, confirming instant display updates without visual artifacting or jitter.

---

## How to Build & Run

1. Clone or download this repository.
2. Open **STM32CubeIDE** and select **File > Import > Existing Projects into Workspace**.
3. Connect the RT-Spark STM32F407 board to your computer via USB.
4. Build the project (`Ctrl + B`).
5. Run or Debug the application to flash the firmware onto the target MCU.
