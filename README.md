# AC DC 5V Power Supply
AC DC 5 V power supply (bridge rectifier + reservoir capacitor + zener-referenced BJT pass stage); final build for Cal Poly Pomona ECE 2200L Microelectronic Circuits Lab.

## Specs (tested at output across RL)

- Target: ~5 V DC output, ripple < 10 mV p-p at the load
- No load ripple (output): ~0.7 mV p-p  
- Loaded ripple (output, RL = 50 Ω ≈ 100 mA): ~9.7 mV p-p (meets <10 mV target)

## Contents
- [Executive Summary](#executive-summary)
- [Objective](#objective)
- [Tools](#tools)
- [Safety / Disclaimer](#safety--disclaimer)
- [Build diagram](#build-diagram)
- [Architecture](#architecture)
- [Key Components](#key-components)
- [Key Component Values (BOM snapshot)](#key-component-values-bom-snapshot)
- [Functional Blocks](#functional-blocks-modules)
- [Altium](#altium-schematic--layout-practice)
- [Verification (bench measurements)](#verification-bench-measurements)
- [Results](#results)
- [Conclusion](#conclusion-end-to-end-behavior)
- [Files](#files)

## Executive Summary
Designed and bench tested an AC to DC 5 V power supply using a transformer, bridge rectifier, 1000 µF reservoir capacitor, and a zener-referenced BJT pass stage. Component values were selected and checked in PSpice, then verified on the bench by measuring ripple at the output across the load resistor under no load and loaded conditions. The measured loaded ripple was ~9.7 mV p-p, meeting the <10 mV p-p target.

## Objective
Build an AC to DC power supply that produces ~5 V DC with low ripple (target <10 mV p-p at the load), then document the measured output ripple across the load resistor under no-load and loaded conditions.

## Tools
PSpice (simulation), Oscilloscope, DMM, Altium (training)

## Safety / Disclaimer
This circuit is powered from mains **through an isolated step-down transformer**. Do **not** connect mains directly to a breadboard or PCB. Use appropriate safety practices (isolation transformer, fusing, insulated connectors/enclosure) when working with AC power.

## Build diagram
<img width="510" height="137" alt="Build diagram" src="https://github.com/user-attachments/assets/c7ebd7e7-aab3-470e-b646-784085b52fee" />

## Architecture
Transformer → Bridge Rectifier → Reservoir Capacitor → Zener-Referenced BJT Pass Stage → ~5 V Output

## Key Components
- **Bridge (4× 1N4001):** full-wave rectification from the transformer secondary.
- **Reservoir capacitor (1000 µF):** reduces ripple by supplying current between rectified peaks.
- **Zener (1N752) & R2 (100 Ω):** sets an approximate reference at the BJT base.
- **BJT pass transistor (2N2222):** emitter follower that provides current gain; output ≈ Vz − Vbe.
- **Load (RL = 50 Ω):** test load ≈ 100 mA at ~5 V.

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
**Input / Step-Down (Mains to Transformer).** The supply begins with a step-down transformer that converts 120 VAC (60 Hz) mains into a low-voltage AC secondary (6.3 VAC RMS). This stage provides isolation from mains and establishes the available AC amplitude that all downstream rectification, filtering, and regulation must work with.

**Rectification (Diode Bridge).** The four 1N4001 diodes are arranged as a full-wave bridge rectifier to convert the transformer secondary into pulsating DC. Because two diodes conduct on each half-cycle, the rectified waveform is reduced by approximately two forward drops, and the ripple component occurs at roughly twice the line frequency (~120 Hz).

**Reservoir / Smoothing (1000 µF).** A 1000 µF reservoir capacitor is placed at the rectifier output to reduce ripple by charging near the waveform peaks and supplying current between peaks. The amount of ripple observed at this node depends strongly on load current: higher load draws the capacitor down further between peaks, increasing the ripple amplitude.

**Output Stage + Load (Series R1, Zener Reference, BJT Pass Stage).** R1 (5 Ω) sits between the reservoir node and the output stage, limiting surge/inrush and adding isolation between the smoothing capacitor and the output stage. The output stage uses a 1N752 zener diode and bias resistor (R2) to establish an approximately constant base reference for the 2N2222 NPN transistor. The transistor operates as a series pass element (emitter-follower behavior), providing current gain to drive the load while holding the output near a 5V class level (approximately Vz − Vbe). Under load (RL = 50 Ω, ~100 mA), this stage is responsible for improving output stability and reducing the ripple seen at the final output compared to the reservoir node.

## Altium (Schematic / Layout Practice)
Recreated the schematic in Altium as layout practice after simulation and bench validation.

<img width="846" height="286" alt="image" src="https://github.com/user-attachments/assets/b51a8fc5-95ef-4d18-a646-539ab0e8e067" />

## Results
Measured output ripple across RL (50 Ω):
- No-load: ~0.7 mV p-p
- Loaded (~100 mA): ~9.7 mV p-p (meets <10 mV target)

## Verification (bench measurements)
All measurements shown here were taken at the output across the load resistor (RL = 50 Ω). Ripple is reported as peak to peak across the load resistor.

Test conditions:
1) No-load (RL disconnected)
2) Loaded (RL = 50 Ω ≈ 100 mA)

Depending on scope settings (especially AC coupling), the display can emphasize the ripple component, which appears near 120 Hz (full-wave rectified line frequency).

Intermediate nodes were checked qualitatively during bring-up, but only the output across RL was captured and included here.

**No Load**

<img width="634" height="402" alt="image" src="https://github.com/user-attachments/assets/22744fbe-6992-407a-af28-059e86c2c18e" />

Interpretation: With minimal load current, the reservoir capacitor droops less between peaks, so there’s less ripple for the output stage to attenuate, resulting in very low ripple across the load.

**Load**

<img width="629" height="396" alt="image" src="https://github.com/user-attachments/assets/b736f66a-68e9-43f0-9444-6ec6c9584f79" />

Interpretation: Under load (~100 mA), the reservoir capacitor discharges more between rectified peaks, increasing the ripple presented to the output stage. The zener/BJT stage attenuates this at the output across the load, and the measured ripple remains below the 10 mV peak to peak target.

## Conclusion (End to End Behavior)
From the wall outlet, the transformer steps 120 VAC down to an isolated low voltage AC secondary, establishing the input amplitude for the supply. The diode bridge then performs fullwave rectification, converting the AC sine into a pulsating DC waveform with a dominant ripple component near 120 Hz. The 1000 µF reservoir capacitor charges near the rectified peaks and supplies current between peaks, reducing ripple at the DC node; under load, ripple increases because the capacitor discharges more between peaks. Finally, the Zener-referenced BJT pass stage provides a ~5 V output (approximately Vz − Vbe) and improves output stability by attenuating ripple at the load relative to the reservoir node. These output ripple measurements across the load match the expected behavior: load current increases reservoir droop between rectified peaks (higher ripple), while the Zener referenced BJT pass stage attenuates that ripple at the output. The scope captures included in this README correspond to the output behavior under no load vs ~100 mA load, validating the design target at the load.

## Files
- `docs/` — source PDFs
