# STM32F401 Robot Control System (L298N + ESP-01 + XL4015)

## Overview

This project implements a motor control and wireless interface system using an STM32F401 microcontroller (Black Pill), an L298N dual H-bridge motor driver, and an ESP-01 (ESP8266) module for communication. The system is powered from a battery through an XL4015 buck converter and includes proper regulation, filtering, and protection components for stability.

The design supports:

* Differential drive using two motor channels (left and right)
* RC receiver input (3-channel PWM)
* WiFi communication via ESP-01 (UART)
* Stable power distribution for mixed loads (motors + logic)

---

## Hardware Components

### Core

* STM32F401CDU6 (Black Pill)
* L298N Motor Driver (single module)
* ESP-01 (ESP8266)
* XL4015 Buck Converter (5V output)
* AMS1117-3.3V regulator

### Power

* 7.4V–12V battery (2S/3S Li-ion recommended)

### Passive Components (SMD 0805 unless noted)

* Resistors:

  * 220Ω (LED current limiting)
  * 1kΩ (signal protection)
  * 10kΩ (pull-ups)
* Capacitors:

  * 100nF (decoupling)
  * 10µF (local regulation)
  * 100µF (bulk on 5V rail)
  * 470µF (motor supply stabilization)
* Diodes:

  * SS34 (reverse polarity protection)
  * Optional motor suppression diodes

---

## System Architecture

Battery power is split into two paths:

* Direct supply to L298N motor driver (high current path)
* XL4015 buck converter to generate a regulated 5V rail

The 5V rail powers:

* STM32 (via 5V pin)
* RC receiver
* AMS1117 regulator (to produce 3.3V for ESP-01)

The ESP-01 communicates with STM32 via UART (USART2).

---

## Pin Configuration

### STM32F401 Mapping

#### Motor Driver (L298N)

| Function        | Pin  |
| --------------- | ---- |
| ENA (PWM Left)  | PA8  |
| IN1             | PB0  |
| IN2             | PB1  |
| ENB (PWM Right) | PA9  |
| IN3             | PB10 |
| IN4             | PB12 |

#### ESP-01 (UART2)

| STM32    | ESP-01 |
| -------- | ------ |
| PA2 (TX) | RX     |
| PA3 (RX) | TX     |

#### RC Receiver Inputs

| Channel | Pin |
| ------- | --- |
| CH1     | PA0 |
| CH2     | PA1 |
| CH3     | PA4 |

---

## ESP-01 Configuration

Required pull-ups:

* CH_PD (EN) → 10k → 3.3V
* GPIO0 → 10k → 3.3V

Power:

* VCC → 3.3V (from AMS1117)
* GND → common ground

Recommended:

* 10µF + 100nF capacitors near VCC

---

## Power Design

### XL4015 Buck Converter

* Input: Battery (7.4V–12V)
* Output: Regulated 5V (must be adjusted before use)

### AMS1117 Regulator

* Input: 5V
* Output: 3.3V (ESP-01 supply)

Required capacitors:

* Input: 10µF
* Output: 10µF

---

## Motor Configuration

* Two motors per side connected in parallel:

  * OUT1/OUT2 → Left motors
  * OUT3/OUT4 → Right motors

Note:

* L298N supports up to approximately 2A peak per channel
* Ensure motors are within current limits

---

## Decoupling and Filtering

Required placement:

* 470µF capacitor near L298N supply input
* 100µF + 100nF on 5V rail near STM32
* 10µF + 100nF near ESP-01
* 100nF across each motor (recommended for noise suppression)

---

## Grounding

All grounds must be connected together:

* Battery GND
* L298N GND
* XL4015 GND
* STM32 GND
* ESP GND
* RC receiver GND

Use a low-impedance ground layout (preferably star or plane in PCB).

---

## Notes and Limitations

* L298N has significant voltage drop (~2V), reducing motor efficiency
* Not suitable for high-current motors
* Ensure proper heat dissipation for L298N
* ESP-01 is sensitive to voltage fluctuations; stable 3.3V is critical

---

## Safety Recommendations

* Add reverse polarity protection diode on battery input
* Use a fuse on the main power line
* Verify XL4015 output voltage before connecting electronics
* Keep high-current motor wiring separate from logic signals

---

## Future Improvements

* Replace L298N with a more efficient driver (e.g., BTS7960 or MOSFET-based driver)
* Add current sensing
* Implement closed-loop motor control
* Design a dedicated PCB with proper ground planes and routing

---

## License

This project is provided for educational and experimental purposes. Use at your own risk.
