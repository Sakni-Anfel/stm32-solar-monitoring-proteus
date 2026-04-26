# STM32 Solar Panel Current Monitor — Proteus Simulation

STM32F401-based solar panel monitoring system simulating real-time current measurement using an ACS712 current sensor, with auto-calibration on startup and live display on a 16×2 LCD — fully simulated in Proteus 8 Professional.

---

## What it does

The system reads current from a solar panel circuit via an ACS712 hall-effect current sensor, processes the ADC readings on an STM32F401, and displays the result in real time on an LM016L LCD.

On startup, the firmware performs an **automatic zero-offset calibration** — it samples the ADC 50 times to compute the sensor's baseline voltage and compensates for it in all subsequent measurements. This ensures accurate readings regardless of supply voltage tolerances.

---

## Hardware (simulated in Proteus)

| Component | Description |
|---|---|
| STM32F401RE | Main microcontroller (ARM Cortex-M4) |
| ACS712 (5A) | Hall-effect current sensor |
| LM016L | 16×2 character LCD |
| IRLZ44N MOSFET | Switching element in the solar circuit |
| LM2595-12 | DC-DC buck converter |
| Solar Panel | Simulated PV source |
| L1 (47µH) + C1/C2 (470µF) | LC filter |
| UF4001 Diode | Freewheeling diode |
| LED (green) | Status indicator |

---

## Firmware overview

Written in C using STM32 HAL (STM32CubeIDE). Key parameters:

| Parameter | Value |
|---|---|
| ADC resolution | 12-bit |
| ADC reference voltage | 3.3 V |
| ADC channel | CH9 (PB1) |
| Sampling mode | Continuous |
| Samples per reading | 20 |
| ACS712 sensitivity | 185 mV/A (5A model) |
| Calibration samples | 50 |

### Startup sequence
1. ADC and GPIO initialized
2. LCD initialized and cleared
3. **Auto-calibration** — 50 ADC samples averaged to compute `zero_offset`
4. LCD displays `"Solar Monitor"` then clears
5. Main loop begins

### Main loop
```c
current_A = Read_ACS712_Instantaneous();
LCD_Set_Cursor(0, 0);
sprintf(lcd_buffer, "I: %.2f A   ", current_A);
LCD_Print(lcd_buffer);
HAL_Delay(500);
```

### Current calculation
```c
float avg_voltage = sum_voltage / SAMPLES;
float current = (avg_voltage - zero_offset) / SENSITIVITY;
if (current < 0.0f) current = 0.0f;  // clip negative noise
```

---

## Simulation screenshots

**Startup — calibration screen**
![Calibration](results/calibration.png)
LCD shows `"Solar Monitor"` and offset voltage (`Offset: 0.000V`) during the auto-calibration phase.

**Running — live current reading**
![Running](results/running.png)
LCD displays instantaneous current: `I: 0.43 A`. Simulation running at 53% CPU load, confirming active ADC sampling.

---

## Project structure

```
stm32-solar-monitoring-proteus/
├── solarmonitoring.pdsprj   ← Proteus project file
├── main.c                   ← STM32 HAL firmware
├── lcd.h                    ← LCD driver header
├── README.md
└── results/
    ├── calibration.png
    └── running.png
```

---

## Requirements

- Proteus 8 Professional (to open `.pdsprj`)
- STM32CubeIDE (to build/modify firmware)
- STM32F4 HAL library

---

## Context

One of my early embedded systems projects — STM32 bare-metal firmware with ADC signal conditioning, sensor calibration, and LCD output, all validated in Proteus simulation before any hardware. Built at ISIMG, Institut Supérieur d'Informatique et de Multimédia de Gabès.

Built at ISIMG, Institut Supérieur d'Informatique et de Multimédia de Gabès.
