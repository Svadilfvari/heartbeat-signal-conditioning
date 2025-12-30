# Heartbeat Measuring Electronic Circuit (Analog Front-End)

This repository documents a hardware project that measures heartbeat signals using a **reflective IR sensor** and an **analog signal-conditioning chain** (filtering + amplification), with a conceptual path toward **BPM counting**.

> **Note:** This is a documentation-only repository (original code/simulation files were not retained). The included report and figures provide the design details and results.

## What I built

- Heartbeat sensing using a reflective IR sensor (CNY70)
- Signal characterization (time-domain + FFT)
- Band-pass filtering around the heartbeat band (example response shown between **0.7–7 Hz**)
- Non-inverting op-amp amplification stage (TL082)
- Conceptual digital counting stage (counter) for BPM

## Contents

- **Report (PDF):** [`docs/Heartbeat_Measuring_Electronic_Circuit.pdf`](docs/Heartbeat_Measuring_Electronic_Circuit.pdf)
- **Figures extracted from the report:** [`images/`](images/)

## Key figures (from the report)

### Figure 1: CNY70 Sensor

![](images/fig_1.png)

### Figure 1.1: Output voltage of our sensor, with R=100 ohm

![](images/fig_1_1.png)

### Figure 1.2: FFT of our signal

![](images/fig_1_2.png)

### Figure 1.3: Frequency band of the sensor signal, tending towards 10Hz

![](images/fig_1_3.png)

### Figure 1.4: Physical sensor and its schematic

![](images/fig_1_4.png)

### Figure 1.5: Load line of the phototransistor

![](images/fig_1_5.png)

### Figure 1.6: Unfiltered heartbeat signal

![](images/fig_1_6.png)

### Figure 2.1: Filter with impedance adaptation

![](images/fig_2_1.png)

### Figure 2.2: Band-pass filter response between 0.7Hz and 7Hz

![](images/fig_2_2.png)

### Figure 2.3: Filter with impedance adaptation, same pass-band

![](images/fig_2_3.png)

### Figure 2.4: MATLAB simulation of ideal vs. practical band-pass filter

![](images/fig_2_4.png)

### Figure 2.5: Practical filter implementation and oscilloscope output

![](images/fig_2_5.png)

### Figure 3.1: Non-inverting amplifier TL082

![](images/fig_3_1.png)

### Figure 3.2: Gain-bandwidth relationship in simulation

![](images/fig_3_2.png)

### Figure 3.3: Amplifier implementation in PSpice

![](images/fig_3_3.png)

### Figure 3.4: Amplified heartbeat signal on oscilloscope

![](images/fig_3_4.png)

### Figure 4.1: 4-bit counter schematic in PSpice

![](images/fig_4_1.png)


## Disclaimer

This project is for educational purposes only and **not** a medical device.
