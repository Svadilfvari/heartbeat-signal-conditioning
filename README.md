# IR Heartbeat Monitor — Analog Front-End (CNY70)

A small hardware project that captures a fingertip heartbeat signal using a **reflective IR sensor (CNY70)** and conditions it with an **analog front-end**:

**sensor → biasing/AC coupling → band‑pass filter → op‑amp gain → (future) comparator → (future) counter/BPM**

> **Repository note:** this is a **documentation repo**. The original simulation/code files were not retained, but the full report + extracted figures are included.

---

## Table of contents

- [Project overview](#project-overview)
- [System overview](#system-overview)
- [1) Sensor stage (CNY70)](#1-sensor-stage-cny70)
- [2) Band-pass filter stage](#2-band-pass-filter-stage)
- [3) Amplifier stage (TL082)](#3-amplifier-stage-tl082)
- [4) BPM counting (concept / next step)](#4-bpm-counting-concept--next-step)
- [Results](#results)
- [Tools & components](#tools--components)
- [Repository structure](#repository-structure)
- [How you could reproduce it](#how-you-could-reproduce-it)
- [Limitations & lessons learned](#limitations--lessons-learned)
- [Disclaimer](#disclaimer)

---

## Project overview

**Goal:** Measure heartbeat-related variations in reflected infrared light from a finger and produce a clean, amplified waveform suitable for peak detection / BPM measurement.

What this repo demonstrates:

- Optical heartbeat sensing principle (reflective IR)
- Signal characterization (time domain + FFT)
- Designing a band‑pass filter around the heartbeat band
- Handling filter loading via impedance adaptation (buffer/follower)
- Op‑amp amplification with gain–bandwidth trade‑offs
- A conceptual digital path to BPM counting

---

## System overview

```mermaid
flowchart LR
  A["CNY70 Reflective IR Sensor"] --> B["Phototransistor Biasing<br/>(set linear region)"]
  B --> C["AC coupling / remove DC"]
  C --> D["Band-pass filter<br/>(~0.7–7 Hz)"]
  D --> E["Op-amp gain (TL082)<br/>~100x"]
  E --> F["(Next step)<br/>Comparator + hysteresis"]
  F --> G["(Next step)<br/>Counter / BPM computation"]

```

---

## 1) Sensor stage (CNY70)

### Principle

A CNY70 contains an **IR LED + phototransistor**. IR light is emitted into the finger and partially reflected back. As blood volume changes during each heartbeat, the finger’s reflectivity changes slightly, causing a small variation in the phototransistor signal.

**Figure: CNY70 sensor**
![](images/fig_1.png)

### Raw signal & frequency band

The sensor output shows heartbeat peaks. To choose filter cutoffs, the signal is analyzed using an **FFT**, revealing that useful energy lies in the low-frequency band (heartbeat range).

**Raw output example**
![](images/fig_1_1.png)

**FFT**
![](images/fig_1_2.png)

**Observed band**
![](images/fig_1_3.png)

### Practical biasing (linear region)

To obtain a more linear phototransistor response, the sensor is biased using a potentiometer and load-line approach, targeting a **collector–emitter voltage (VCE)** that places operation in a linear region.

**Physical sensor + schematic**
![](images/fig_1_4.png)

**Load line**
![](images/fig_1_5.png)

**Unfiltered heartbeat on oscilloscope**
![](images/fig_1_6.png)

---

## 2) Band-pass filter stage

### Why a band-pass filter?

Real measurements are noisy (ambient electrical/optical interference, movement, etc.). A band‑pass filter helps:

- Remove the **DC component** (via the high‑pass portion)
- Reduce low-frequency drift and high-frequency noise
- Preserve heartbeat-related content in a narrow band

### Theory: RC + CR filter and impedance adaptation

A practical RC/CR band‑pass can suffer from **stage coupling/loading**. To make the response closer to the ideal product of the two transfer functions, an **impedance adaptation** stage is used (buffer/follower or a very large resistor).

**Filter with impedance adaptation**
![](images/fig_2_1.png)

### Chosen pass-band (~0.7–7 Hz)

A design example shown in the report uses an RC‑CR filter with values that yield a band‑pass response around **0.7 Hz to 7 Hz**.

**Band-pass response**
![](images/fig_2_2.png)

**Same pass-band with impedance adaptation**
![](images/fig_2_3.png)

### Simulation validation (MATLAB)

The report compares the ideal response (H3 ≈ H1·H2) with the follower-based implementation.

**Ideal vs practical response**
![](images/fig_2_4.png)

### Practical test (scope)

The filter is tested on breadboard. The report shows **green = raw** and **yellow = filtered** heartbeat waveform.

![](images/fig_2_5.png)

---

## 3) Amplifier stage (TL082)

### Why amplify?

After filtering, the heartbeat component can be on the order of **tens of millivolts**, so amplification is required for clear viewing and eventual conversion to digital pulses.

### Non‑inverting amplifier (gain selection)

A **TL082** non‑inverting amplifier is used:

- Gain: **1 + R2/R1**
- Practical constraint: **gain–bandwidth product**, so high gain reduces available bandwidth

**Non‑inverting TL082 schematic**
![](images/fig_3_1.png)

**Gain vs bandwidth trend**
![](images/fig_3_2.png)

A gain of approximately **100×** is selected as a good trade‑off for this application.

### Practical build & measured output

**Amplifier implementation**
![](images/fig_3_3.png)

**Amplified heartbeat on oscilloscope**
![](images/fig_3_4.png)

---

## 4) BPM counting (concept / next step)

To compute BPM, you need clean digital pulses: one pulse per heartbeat.

### Comparator (planned)

The report proposes adding a comparator stage that:

- Uses an **adjustable threshold**
- Rejects noise using **hysteresis** (Schmitt trigger behavior)

Then the comparator output can drive a counter.

### Counter (PSpice design)

A simple **4‑bit counter** is shown as a proof-of-concept.

![](images/fig_4_1.png)

---

## Results

What the report demonstrates visually:

- A heartbeat waveform is visible at the sensor output.
- The filter removes a significant amount of noise and DC drift.
- The amplified waveform is clearly observable on the oscilloscope, suitable for a comparator stage.

---

## Tools & components

**Hardware**
- CNY70 reflective IR sensor
- TL082 operational amplifier
- Passive components (R/C) for the band‑pass filter
- Breadboard, lab supply, oscilloscope

**Software**
- MATLAB (filter/signal validation in the report)
- PSpice (circuit simulations and counter schematic)

---

## Repository structure

- `docs/Heartbeat_Measuring_Electronic_Circuit.pdf` — full project report
- `images/` — figures extracted from the report (used in this README)

---

## How you could reproduce it

High-level steps (hardware-lab friendly):

1. **Wire the CNY70** and measure the raw output while holding a finger over the sensor.
2. **Bias the phototransistor** into a linear region (use a potentiometer and confirm VCE on a multimeter).
3. Use **AC coupling** on the scope (or a coupling capacitor) to remove DC and highlight pulsations.
4. Build the **band‑pass filter** (target a low-frequency pass band roughly around the heartbeat range).
5. Add a **buffer/follower** stage if the RC stages load each other and the response shifts.
6. Add the **TL082 non‑inverting amplifier** and tune gain (≈100× works well per the report’s trade‑off discussion).
7. *(Next step)* Add a **Schmitt trigger comparator** to create one square pulse per beat.
8. *(Next step)* Count pulses over a time window to compute **BPM**.

---

## Limitations & lessons learned

- Real-world optical measurements are noisy; filtering is essential before trying to “count beats.”
- Passive filter stages can load each other; a buffer/follower makes the response behave more like the ideal design.
- Amplifier gain must be chosen with bandwidth in mind (gain–bandwidth tradeoff).
- A comparator with hysteresis is the key missing block to convert an analog waveform into reliable digital pulses.

---

## Disclaimer

This project is for **educational purposes** only and is **not a medical device**.
