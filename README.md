# AC–DC 5V Power Supply
AC–DC 5V power supply (rectifier + smoothing + regulation); final build for Cal Poly Pomona ECE 2200L Microelectronic Circuits Lab.

## Executive Summary
This project designs and bench-evaluates an AC–DC 5V power supply using a transformer, diode bridge rectifier, reservoir capacitor, and a simple zener/BJT regulation stage. The circuit was first modeled in PSpice to select key component values and estimate ripple behavior, then validated on the bench by probing each stage from the transformer output through the regulated load. The design goal was a stable ~5VDC output with low ripple under load, confirmed using oscilloscope measurements.

## Objective
Build an AC–DC supply that produces a stable ~5VDC output with low ripple (target <10 mV p-p at the load) and document waveform behavior across each stage: rectification, smoothing, and regulation.

## Build diagram
<img width="510" height="137" alt="image" src="https://github.com/user-attachments/assets/c7ebd7e7-aab3-470e-b646-784085b52fee" />

## Components (Device-level concepts used in this build)
**Diode (1N4001).** A diode primarily conducts in one direction (forward bias) and blocks current in the reverse direction (reverse bias) up to its breakdown limits. In forward conduction it exhibits an approximately constant forward drop and a non-linear I–V curve, which matters in rectifier stages because it reduces the available DC headroom.

**Capacitor (1000 µF).** A capacitor stores energy in an electric field and resists rapid changes in voltage. In power supplies it is commonly used as a reservoir (smoothing) capacitor: it charges near the peaks of the rectified waveform and then supplies current between peaks, reducing ripple at the output of the rectifier.

**Resistor (R1 = 5 Ω, R2 = 100 Ω, RL = 50 Ω).** Resistors set currents and voltage drops. In this circuit, R1 provides series resistance between the reservoir node and the regulation stage (helping limit surge and isolate stages), R2 biases the zener/reference and transistor base node, and RL represents the load used for testing (≈100 mA at ~5 V).

**BJT (2N2222, NPN).** A bipolar junction transistor uses a small base current to control a larger collector–emitter current. In this supply it functions as a series pass element (emitter follower behavior), providing current gain so the load can be driven while the reference network sets the approximate output voltage.

**Zener diode (1N752).** A zener diode is designed to operate in reverse breakdown to provide an approximately constant reference voltage over a range of current. In this circuit it provides a voltage reference at the transistor base; the output is then approximately the zener voltage minus the transistor’s base–emitter drop.

**Transformer (120 V : 6.3 V).** The transformer provides galvanic isolation and steps mains voltage down to a low-voltage AC source. The secondary is rated in RMS and remains sinusoidal at 60 Hz, forming the input to the rectification stage.

## Architecture
Transformer → Bridge Rectifier → Smoothing Capacitor → Regulation Stage → 5 V Output

## Functional Blocks (Modules)
**Input / Step-Down (Mains → Transformer).** The supply begins with a step-down transformer that converts 120 VAC (60 Hz) mains into a low-voltage AC secondary (6.3 VAC RMS). This stage provides isolation from mains and establishes the available AC amplitude that all downstream rectification, filtering, and regulation must work with.

**Rectification (Diode Bridge).** The four 1N4001 diodes are arranged as a full-wave bridge rectifier to convert the transformer secondary into pulsating DC. Because two diodes conduct on each half-cycle, the rectified waveform is reduced by approximately two forward drops, and the ripple component occurs at roughly twice the line frequency (~120 Hz).

**Reservoir / Smoothing (1000 µF).** A 1000 µF reservoir capacitor is placed at the rectifier output to reduce ripple by charging near the waveform peaks and supplying current between peaks. The amount of ripple observed at this node depends strongly on load current: higher load draws the capacitor down further between peaks, increasing the ripple amplitude.

**Regulation + Load (Series R1 + Zener Reference + BJT Pass Stage).** R1 (5 Ω) sits between the reservoir node and the regulator section, limiting surge/inrush and adding isolation between the smoothing capacitor and the regulation stage. The regulation stage uses a 1N752 zener diode and bias resistor (R2) to establish an approximately constant base reference for the 2N2222 NPN transistor. The transistor operates as a series pass element (emitter-follower behavior), providing current gain to drive the load while holding the output near a 5 V-class level (approximately Vz − Vbe). Under load (RL = 50 Ω, ~100 mA), this stage is responsible for improving output stability and reducing the ripple seen at the final output compared to the reservoir node.

## Tools
PSpice (simulation) • Oscilloscope + DMM (bench validation)

## Bring-up & Testing
Bench testing with oscilloscope and DMM; troubleshooting and characterization across each stage.

**No Load**

<img width="634" height="402" alt="image" src="https://github.com/user-attachments/assets/22744fbe-6992-407a-af28-059e86c2c18e" />

**Load**

<img width="629" height="396" alt="image" src="https://github.com/user-attachments/assets/b736f66a-68e9-43f0-9444-6ec6c9584f79" />

## Results (Measured)
- **No-load ripple:** ~0.7 mV p-p  
- **Loaded ripple (RL = 50 Ω, ~100 mA):** ~9.7 mV p-p (meets <10 mV target)

## Conclusion (End-to-End Behavior)
From the wall outlet, the transformer steps 120 VAC (60 Hz) down to a low-voltage AC secondary, establishing the input amplitude for the supply. The diode bridge then performs full-wave rectification, converting the AC sine into a pulsating DC waveform with a dominant ripple component near 120 Hz. The 1000 µF reservoir capacitor charges near the rectified peaks and supplies current between peaks, reducing ripple at the DC node; under load, ripple increases because the capacitor discharges more between peaks. Finally, the zener-referenced BJT pass stage provides a 5 V-class regulated output (approximately Vz − Vbe) and improves output stability by attenuating ripple at the load relative to the reservoir node.

The oscilloscope captures document this progression stage-by-stage: the rectifier output shows full-wave pulsating DC, the reservoir node shows reduced ripple after smoothing, and the regulated output shows the lowest ripple at the load. The measured ripple values—~0.7 mV p-p no-load and ~9.7 mV p-p at RL = 50 Ω (~100 mA)—demonstrate the expected relationship between load current and ripple while confirming the <10 mV design target under load.


## Files
- `docs/` — source PDFs (kept private)
- `assets/` — photos and scope captures
