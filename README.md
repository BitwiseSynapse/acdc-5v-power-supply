# AC DC 5V Power Supply
AC DC 5V power supply (rectifier + smoothing + regulation); final build for Cal Poly Pomona ECE 2200L Microelectronic Circuits Lab.

## Contents
- [Executive Summary](#executive-summary)
- [Objective](#objective)
- [Tools](#tools)
- [Safety / Disclaimer](#safety--disclaimer)
- [Build diagram](#build-diagram)
- [Architecture](#architecture)
- [Components](#components)
- [Functional Blocks](#functional-blocks-modules)
- [Test Points](#test-points)
- [Altium](#altium-schematic--layout-practice)
- [Bring-up & Testing](#bring-up--testing)
- [Results](#results-measured-at-output--tp3)
- [Conclusion](#conclusion-end-to-end-behavior)
- [Files](#files)

## Executive Summary
This project designs and bench-evaluates an AC DC 5V power supply using a transformer, diode bridge rectifier, reservoir capacitor, and a simple zener/BJT regulation stage. The circuit was first modeled in PSpice to select key component values and estimate ripple behavior, then validated on the bench by probing each stage from the transformer output through the regulated load. The design goal was a stable ~5VDC output with low ripple under load, confirmed using oscilloscope measurements.

## Objective
Build an AC DC supply that produces a stable ~5VDC output with low ripple (target <10 mV p-p at the load) and document waveform behavior across each stage: rectification, smoothing, and regulation.

## Tools
PSpice (simulation) • Oscilloscope + DMM (bench validation) • Altium (training)

## Safety / Disclaimer
This circuit is powered from mains **through an isolated step-down transformer**. Do **not** connect mains directly to a breadboard or PCB. Use appropriate safety practices (isolation transformer, fusing, insulated connectors/enclosure) when working with AC power.

## Build diagram
<img width="510" height="137" alt="Build diagram" src="https://github.com/user-attachments/assets/c7ebd7e7-aab3-470e-b646-784085b52fee" />

## Architecture
Transformer → Bridge Rectifier → Smoothing Capacitor → Regulation Stage → 5 V Output

## Components
**Diode (1N4001).** A diode primarily conducts in one direction (forward bias) and blocks current in the reverse direction (reverse bias) up to its breakdown limits. In forward conduction it exhibits an approximately constant forward drop and a non-linear I–V curve, which matters in rectifier stages because it reduces the available DC headroom.

**Capacitor (1000 µF).** A capacitor stores energy in an electric field and resists rapid changes in voltage. In power supplies it is commonly used as a reservoir (smoothing) capacitor: it charges near the peaks of the rectified waveform and then supplies current between peaks, reducing ripple at the output of the rectifier.

**Resistor (R1 = 5 Ω, R2 = 100 Ω, RL = 50 Ω).** Resistors set currents and voltage drops. In this circuit, R1 provides series resistance between the reservoir node and the regulation stage (helping limit surge and isolate stages), R2 biases the zener/reference and transistor base node, and RL represents the load used for testing (≈100 mA at ~5 V).

**BJT (2N2222, NPN).** A bipolar junction transistor uses a small base current to control a larger collector–emitter current. In this supply it functions as a series pass element (emitter follower behavior), providing current gain so the load can be driven while the reference network sets the approximate output voltage.

**Zener diode (1N752).** A zener diode is designed to operate in reverse breakdown to provide an approximately constant reference voltage over a range of current. In this circuit it provides a voltage reference at the transistor base; the output is then approximately the zener voltage minus the transistor’s base–emitter drop.

**Transformer (120 V : 6.3 V).** The transformer provides galvanic isolation and steps mains voltage down to a low-voltage AC source. The secondary is rated in RMS and remains sinusoidal at 60 Hz, forming the input to the rectification stage.

## Key Component Values (BOM snapshot)
- **Transformer:** 120 VAC → 6.3 VAC RMS (isolated)
- **Bridge diodes:** 4× 1N4001
- **Reservoir capacitor:** 1000 µF
- **R1:** 5 Ω (series / surge limiting)
- **R2:** 100 Ω (zener / base bias)
- **Zener:** 1N752
- **Pass transistor:** 2N2222 (NPN)
- **Load:** 50 Ω (≈100 mA at 5 V)

## Functional Blocks (Modules)
**Input / Step-Down (Mains → Transformer).** The supply begins with a step-down transformer that converts 120 VAC (60 Hz) mains into a low-voltage AC secondary (6.3 VAC RMS). This stage provides isolation from mains and establishes the available AC amplitude that all downstream rectification, filtering, and regulation must work with.

**Rectification (Diode Bridge).** The four 1N4001 diodes are arranged as a full-wave bridge rectifier to convert the transformer secondary into pulsating DC. Because two diodes conduct on each half-cycle, the rectified waveform is reduced by approximately two forward drops, and the ripple component occurs at roughly twice the line frequency (~120 Hz).

**Reservoir / Smoothing (1000 µF).** A 1000 µF reservoir capacitor is placed at the rectifier output to reduce ripple by charging near the waveform peaks and supplying current between peaks. The amount of ripple observed at this node depends strongly on load current: higher load draws the capacitor down further between peaks, increasing the ripple amplitude.

**Regulation + Load (Series R1 + Zener Reference + BJT Pass Stage).** R1 (5 Ω) sits between the reservoir node and the regulator section, limiting surge/inrush and adding isolation between the smoothing capacitor and the regulation stage. The regulation stage uses a 1N752 zener diode and bias resistor (R2) to establish an approximately constant base reference for the 2N2222 NPN transistor. The transistor operates as a series pass element (emitter-follower behavior), providing current gain to drive the load while holding the output near a 5 V-class level (approximately Vz − Vbe). Under load (RL = 50 Ω, ~100 mA), this stage is responsible for improving output stability and reducing the ripple seen at the final output compared to the reservoir node.

## Altium (Schematic / Layout Practice)
Recreated the schematic in Altium as layout practice after simulation and bench validation.

<img width="846" height="286" alt="image" src="https://github.com/user-attachments/assets/b51a8fc5-95ef-4d18-a646-539ab0e8e067" />

## Test Points
To correlate measurements with circuit behavior, these nodes are useful probe locations:

- **TP1 — Transformer secondary (AC1/AC2):** ~6.3 VAC RMS, 60 Hz sine before rectification.
- **TP2 — Rectifier output / Reservoir node (after bridge + before regulation):** full-wave pulsating DC (~120 Hz ripple) and capacitor charge/discharge behavior.
- **TP3 — Regulated output (across RL):** ~5 V-class DC (≈ Vz − Vbe) with minimized ripple.

## Bring-up & Testing
Validated the final build on the bench using an oscilloscope and DMM. The output node (TP3) was probed under no-load and under a 50 Ω load to measure DC output level and output ripple.

## Results (Measured at Output / TP3)
- **No-load ripple:** ~0.7 mV p-p  
- **Loaded ripple (RL = 50 Ω, ~100 mA):** ~9.7 mV p-p (**meets <10 mV target**)

## Notes
- Ripple values are reported as **peak-to-peak** at the **regulated output (TP3)**.
- After full-wave rectification, the dominant ripple component is near **120 Hz** (2× line frequency).

**No Load**

<img width="634" height="402" alt="image" src="https://github.com/user-attachments/assets/22744fbe-6992-407a-af28-059e86c2c18e" />

**Interpretation:** With minimal load current, the supply draws less current, so the reservoir capacitor droops less between peaks and the regulator has less ripple/variation to reject—resulting in very low ripple at the **output (TP3)**.

**Load**

<img width="629" height="396" alt="image" src="https://github.com/user-attachments/assets/b736f66a-68e9-43f0-9444-6ec6c9584f79" />

**Interpretation:** Under load (~100 mA), the reservoir capacitor discharges more between rectified peaks, which increases the ripple presented to the regulator. The zener/BJT stage attenuates this at the **output (TP3)**, and the measured ripple remains below the 10 mV p-p target.

## Conclusion (End-to-End Behavior)
From the wall outlet, the transformer steps 120 VAC down to an isolated low-voltage AC secondary, establishing the input amplitude for the supply. The diode bridge then performs full-wave rectification, converting the AC sine into a pulsating DC waveform with a dominant ripple component near 120 Hz. The 1000 µF reservoir capacitor charges near the rectified peaks and supplies current between peaks, reducing ripple at the DC node; under load, ripple increases because the capacitor discharges more between peaks. Finally, the zener-referenced BJT pass stage provides a 5 V-class regulated output (approximately Vz − Vbe) and improves output stability by attenuating ripple at the load relative to the reservoir node. These output ripple measurements (TP3) match the expected behavior: load current increases reservoir droop between rectified peaks (higher ripple), while the zener-referenced BJT pass stage attenuates that ripple at the output. The scope captures included in this README correspond to the regulated output behavior under no-load vs ~100 mA load, validating the design target at the load.

## Files
- `docs/` — source PDFs (kept private)
