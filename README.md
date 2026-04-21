# STM32 Macro Keyboard

A custom 6-key macro keyboard built from scratch custom written bare-metal STM32 firmware, hand-drawn schematics, and a PCB designed in KiCAD. No Arduino. No HAL. Just registers, datasheets, and stubbornness.

> **Status:** Hardware design in progress

---

<!-- Add schematic screenshot here once complete -->
<!-- ![Schematic](docs/schematic.png) -->

<!-- Add PCB render here once routed -->
<!-- ![PCB](docs/pcb_render.png) -->

<!-- Add photo of assembled board here -->
<!-- ![Board](docs/board.jpg) -->

---

## Why

I am learning to design STM32-based hardware - from choosing a microcontroller and designing the PCB, to writing drivers from scratch in register-level C.

## Hardware

| Item | Detail |
|---|---|
| MCU | STM32F072CBTx — Cortex-M0, 48 MHz, 128 KB flash, LQFP-48 |
| Key matrix | 6 switches with diode-per-key (1N4148W) |
| Power | 3.3 V core, ferrite bead on analog rail (VDDA), decoupling on all supply pins |
| Programming | USB DFU via BOOT0 SPDT switch — no debug probe needed |
| EDA tool | KiCAD 10.0 |

---

## Requirements

Requirements follow the systems engineering convention: **shall** = mandatory, **should** = desired.

### SYS — System
| ID | Requirement |
|---|---|
| SYS-01 | The device shall enumerate as a USB HID keyboard on any host OS without requiring driver installation. |
| SYS-02 | The device shall be powered entirely from the USB bus (5 V, ≤ 100 mA). |
| SYS-03 | The device shall support a minimum of 6 independently programmable macro keys. |
| SYS-04 | The device firmware shall be updatable via USB without a hardware debug probe. |
| SYS-05 | All firmware shall be written in register-level C with no vendor HAL or Arduino abstractions. |

### HW — Hardware
| ID | Requirement |
|---|---|
| HW-01 | The MCU core supply (VDD) shall be 3.3 V ± 5%. |
| HW-02 | Each VDD, VDDA, VBAT, and VSSIO2 supply pin shall have a dedicated decoupling capacitor placed within 2 mm of the pin. |
| HW-03 | VDDA shall be filtered from VDD using a ferrite bead to reduce digital noise on the analog supply. |
| HW-04 | Each key switch shall have a dedicated series diode to prevent ghosting in the key matrix. |
| HW-05 | The BOOT0 pin shall be switchable between VDD and GND via an SPDT switch to allow toggling between DFU programming mode and normal run mode. |
| HW-06 | The PCB shall pass KiCAD DRC with zero errors before submission for fabrication. |

### FW — Firmware
| ID | Requirement |
|---|---|
| FW-01 | The firmware shall configure the system clock to 48 MHz using the internal PLL before any peripheral initialisation. |
| FW-02 | The key scan loop shall debounce each key with a settling time of ≤ 5 ms. |
| FW-03 | The firmware shall report a key press to the USB host within 10 ms of a physical key actuation. |
| FW-04 | Macro key mappings shall be stored in non-volatile flash and survive a power cycle. |
| FW-05 | The firmware shall implement the USB HID boot-protocol keyboard descriptor to ensure compatibility with BIOS/UEFI environments. |

### VER — Verification
| ID | Requirement | Method |
|---|---|---|
| VER-01 | All supply rails measure within tolerance at board bring-up. | Test — multimeter |
| VER-02 | Device enumerates as HID keyboard on Windows, macOS, and Linux. | Test — plug in, check device manager |
| VER-03 | Every key registers a unique, correct HID keycode. | Test — key tester utility |
| VER-04 | Firmware can be flashed via DFU using only the BOOT0 switch and a USB cable. | Demonstration |
| VER-05 | Key mappings persist across power cycles. | Test — assign macro, power cycle, verify |
| VER-06 | PCB DRC passes with zero errors. | Analysis — KiCAD DRC report |

---

<details>
<summary><strong>Roadmap</strong></summary>

### Electronics
- [x] MCU symbol + footprint
- [x] Decoupling capacitors (VBAT, VDD, VDDA, VSSIO2)
- [x] BOOT0 SPDT switch for programming / run mode toggle
- [ ] USB data lines + ESD protection
- [ ] USB power input + 3.3 V regulator
- [ ] Key switch matrix (complete all 6 keys)
- [ ] PCB layout and routing
- [ ] DRC pass → Gerber export → fabrication

### Firmware
- [ ] Bare-metal project setup (linker script, startup file)
- [ ] System clock → 48 MHz via PLL
- [ ] GPIO driver — key matrix scan
- [ ] USB HID descriptor (boot-protocol keyboard)
- [ ] Key debounce
- [ ] Macro layer + flash storage for mappings

</details>

---