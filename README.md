# Digital Water Level Indicator using Priority Encoder, Hex Inverter & BCD-to-7-Segment Decoder

**Digital Logic Design (DLD) Lab Project**

A fully combinational-logic water level indicator — no microcontroller, no firmware. Nine sensing probes inside a tank feed a priority encoder, whose output is inverted and decoded to drive a single 7-segment display showing the water level as a digit from **0 (empty)** to **9 (full)**.

IC set: **74HC147** (priority encoder) → **74HC04** (hex inverter) → **CD4511** (BCD-to-7-segment latch/decoder/driver)

![Circuit schematic](schematic.png)

---

## Table of Contents

1. [Objective](#1-objective)
2. [Introduction](#2-introduction)
3. [Components Required](#3-components-required)
4. [Theory of Operation](#4-theory-of-operation)
5. [Working Principle](#5-working-principle)
6. [Circuit Diagram](#6-circuit-diagram)
7. [Pin Connection Guide](#7-pin-connection-guide)
8. [Design and Assembly Procedure](#8-design-and-assembly-procedure)
9. [Observation Table](#9-observation-table)
10. [Applications](#10-applications)
11. [Advantages and Limitations](#11-advantages-and-limitations)
12. [Precautions](#12-precautions)
13. [Conclusion](#13-conclusion)
14. [References](#14-references)

---

## 1. Objective

Design and simulate a Digital Water Level Indicator that displays the exact water level of a tank (0–9, representing 10 discrete levels) on a seven-segment display, using only combinational digital logic ICs — a decimal-to-BCD priority encoder, a hex inverter, and a BCD-to-seven-segment latch/decoder/driver — without any microcontroller or programmable device. The project reinforces core Digital Logic Design concepts including priority encoding, logic-level inversion, BCD code conversion, and seven-segment display driving.

## 2. Introduction

Water tanks are usually monitored manually, which is inconvenient and often leads to overflow or dry-running of the pump. A digital water level indicator solves this by sensing the water level using a set of conducting probes placed at fixed heights inside the tank and converting the sensed level into a readable decimal digit on a display.

The circuit uses nine sensing probes fixed at nine different heights inside the tank (plus the tank base/ground as the common reference). Water, being electrically conductive, closes the circuit between a probe and the common ground line whenever the water level reaches that probe, pulling the corresponding encoder input LOW. The highest probe that is submerged represents the current water level and is displayed as a decimal digit (0–9) on the 7-segment display.

## 3. Components Required

| S.No | Component | Specification / Value | Qty |
|---|---|---|---|
| 1 | Solderless Breadboard | Standard prototyping board | 1 |
| 2 | Voltage Regulator | IC 7805 (+5V, 1A linear regulator) | 1 |
| 3 | Priority Encoder IC | 74HC147 — 10-line to 4-line (Decimal-to-BCD) priority encoder | 1 |
| 4 | Hex Inverter IC | 74HC04 — Six independent NOT gates | 1 |
| 5 | BCD-to-7-Segment Driver IC | CD4511 — BCD to 7-segment latch/decoder/driver | 1 |
| 6 | Seven Segment Display | Common Cathode, single digit | 1 |
| 7 | Pull-up Resistor | 560 kΩ (one per sensor probe line) | 9 |
| 8 | Current-limiting Resistor | 100 Ω | 1 |
| 9 | Filter Capacitor | 0.1 µF ceramic (for regulator input/output) | 2 |
| 10 | Jumper Wires | Male-to-Male | As required |
| 11 | Header | Male header pins | As required |
| 12 | Hook-up Wire | Solid core / hard jumper wire (for probes) | As required |
| 13 | Battery Clip | 9V battery snap connector | 1 |
| 14 | Battery | 9V alkaline battery | 1 |

## 4. Theory of Operation

### 4.1 Decimal-to-BCD Priority Encoder — 74HC147

The 74HC147 is a 10-line-to-4-line priority encoder that accepts nine active-LOW decimal inputs (1–9) and produces a 4-bit active-LOW BCD output (D, C, B, A) representing the complement of the BCD code of the highest-numbered active input. Input '0' is implied — when none of the nine inputs are LOW, the outputs default to the complement of 0000, i.e. all outputs HIGH.

Because it is a *priority* encoder, if more than one input is LOW simultaneously (which happens naturally here, since water shorts every probe below the current level to ground), only the highest-order active input is encoded — exactly the behavior needed to report the true water level rather than a lower, already-submerged probe.

**Truth table of the 74HC147** (H = HIGH, L = LOW, X = don't care; inputs and outputs are active-LOW):

| Active Input | I1 | I2 | I3 | I4 | I5 | I6 | I7 | I8 | I9 | D C B A (out) |
|---|---|---|---|---|---|---|---|---|---|---|
| None | H | H | H | H | H | H | H | H | H | H H H H |
| 1 | L | X | X | X | X | X | X | X | X | H H H L |
| 2 | X | L | X | X | X | X | X | X | X | H H L H |
| 3 | X | X | L | X | X | X | X | X | X | H H L L |
| 4 | X | X | X | L | X | X | X | X | X | H L H H |
| 5 | X | X | X | X | L | X | X | X | X | H L H L |
| 6 | X | X | X | X | X | L | X | X | X | H L L H |
| 7 | X | X | X | X | X | X | L | X | X | H L L L |
| 8 | X | X | X | X | X | X | X | L | X | L H H H |
| 9 | X | X | X | X | X | X | X | X | L | L H H L |

Since the outputs D C B A are the bit-wise complement of the true BCD code, they cannot be fed directly into the BCD-to-7-segment decoder — they must first be inverted.

### 4.2 Hex Inverter — 74HC04

The 74HC04 contains six independent NOT (inverter) gates in a single 14-pin package. Four of its six gates are used here to invert the four active-LOW outputs (D, C, B, A) of the 74HC147, restoring them to the true, active-HIGH BCD code that the CD4511 decoder expects — a practical illustration of Boolean complementation bridging two ICs with opposite logic conventions.

### 4.3 BCD-to-7-Segment Latch/Decoder/Driver — CD4511

The CD4511 accepts a 4-bit BCD input (D C B A, active-HIGH) and drives the seven segments (a–g) of a common-cathode 7-segment display. It also includes three control inputs: Lamp Test (LT), Blanking Input (BI), and Latch Enable (LE).

| LT | BI | LE | D C B A | Display | Segments a–g driven |
|---|---|---|---|---|---|
| H | H | L | 0000 | 0 | a b c d e f |
| H | H | L | 0001 | 1 | b c |
| H | H | L | 0010 | 2 | a b d e g |
| H | H | L | 0011 | 3 | a b c d g |
| H | H | L | 0100 | 4 | b c f g |
| H | H | L | 0101 | 5 | a c d f g |
| H | H | L | 0110 | 6 | a c d e f g |
| H | H | L | 0111 | 7 | a b c |
| H | H | L | 1000 | 8 | a b c d e f g |
| H | H | L | 1001 | 9 | a b c d f g |
| L | X | X | XXXX | 8 (Lamp Test) | all segments ON |
| X | L | X | XXXX | Blank | all segments OFF |
| X | X | H | XXXX | Holds last digit (latched) | unchanged |

### 4.4 Seven-Segment Display (Common Cathode)

A common-cathode seven-segment display has seven LED segments (a–g) sharing one common ground pin. Applying logic HIGH to a segment through the driver IC lights that segment. Combining segments in the patterns above produces the digits 0–9.

### 4.5 Voltage Regulator — IC 7805

The 7805 is a 3-terminal linear voltage regulator that converts the unregulated 9V battery supply into a stable, regulated +5V DC output required by the logic ICs. Two 0.1 µF decoupling capacitors are placed on the input and output pins of the regulator to suppress noise and prevent oscillation.

## 5. Working Principle

- Nine sensing probes (wires) are fixed at nine equally-spaced heights inside the water tank, wired to the nine inputs (I1–I9) of the 74HC147 through 560 kΩ pull-up resistors that hold each input HIGH (inactive) by default.
- A common probe/electrode is placed at the base of the tank and connected to circuit ground.
- As water rises, it electrically bridges the ground probe to each sensing probe it reaches, pulling the corresponding 74HC147 input LOW (active).
- All probes below the current water surface are also submerged and therefore LOW at the same time — but the 74HC147's priority-encoding behavior ensures only the highest-priority active input is encoded, corresponding correctly to the actual water level.
- The 74HC147 outputs the complemented BCD code of that level.
- The 74HC04 hex inverter flips all four bits back to true BCD.
- The CD4511 decodes the true BCD code into the correct seven-segment pattern and drives the display.
- The display shows a digit from 0 (empty tank) to 9 (full tank) — a real-time, purely combinational-logic read-out with no microcontroller or software involved.

## 6. Circuit Diagram

Built and simulated in Proteus Design Suite: nine push-button/probe inputs into the 74HC147, its outputs routed through four gates of the 74HC04, into the CD4511, driving the common-cathode 7-segment display, with the 7805 regulator and battery supplying +5V.

![Digital Water Level Display circuit](schematic.png)

*Proteus project files (`.pdsprj`) and original schematics: [embeddedlab786/Digital_Water_Level_Display](https://github.com/embeddedlab786/Digital_Water_Level_Display)*

## 7. Pin Connection Guide

| From | Pin | To | Pin / Node |
|---|---|---|---|
| Probe 1–9 / Push-buttons | — | 74HC147 | I1–I9 (through 560 kΩ pull-ups to +5V) |
| 74HC147 | Output D | 74HC04 | Inverter gate input 1 |
| 74HC147 | Output C | 74HC04 | Inverter gate input 2 |
| 74HC147 | Output B | 74HC04 | Inverter gate input 3 |
| 74HC147 | Output A | 74HC04 | Inverter gate input 4 |
| 74HC04 | Inverted D,C,B,A outputs | CD4511 | D, C, B, A inputs (pins 2,1,6,7 typical) |
| CD4511 | LT, BI | +5V (Vcc) | tied HIGH for normal decoding |
| CD4511 | LE | GND | tied LOW so display follows input continuously |
| CD4511 | Segment outputs a–g | 7-Segment Display | corresponding segment pins, via 100 Ω resistor |
| 7-Segment Display | Common Cathode | GND | circuit ground |
| 9V Battery | +/− | 7805 IC | Vin / GND |
| 7805 IC | Vout (+5V) | All logic ICs | Vcc rail |
| 0.1 µF Capacitors | ×2 | 7805 | input and output pins, to GND |

## 8. Design and Assembly Procedure

1. Study the datasheets of 74HC147, 74HC04, and CD4511 and note their pin diagrams and active-logic conventions.
2. Place the 74HC147, 74HC04, CD4511, and the 7805 regulator on the breadboard, spaced to allow clear wiring.
3. Wire the 9V battery through the battery clip into the 7805 input, with a 0.1 µF capacitor across input-to-ground and another across output-to-ground.
4. Distribute the regulated +5V and GND rails along the breadboard's power rails.
5. Connect nine 560 kΩ resistors from +5V to each of the nine 74HC147 inputs (I1–I9); connect the far end of each input line to its corresponding probe wire / push-button (other end of the switch to GND to simulate water contact).
6. Wire the four 74HC147 outputs (D, C, B, A) into four separate gates of the 74HC04.
7. Wire the four inverted outputs of the 74HC04 into the D, C, B, A inputs of the CD4511.
8. Tie CD4511's LT and BI pins HIGH (Vcc) and LE pin LOW (GND) for continuous, normal decoding.
9. Connect the seven CD4511 segment outputs (a–g) to the corresponding segments of the common-cathode 7-segment display, through a 100 Ω current-limiting resistor if not already accounted for.
10. Connect the display's common cathode pin to GND.
11. Double-check all IC Vcc/GND pins are powered before switching on.
12. Power the circuit and test each probe/switch individually, then in combination, verifying the displayed digit matches the highest activated probe.

## 9. Observation Table

Record results while testing the circuit (simulate rising water by closing switches 1 through 9 in sequence):

| Water Level (Probe Reached) | 74HC147 Output (D C B A, active-LOW) | After 74HC04 (true BCD) | 7-Segment Display |
|---|---|---|---|
| Empty (no probe touched) | H H H H | 0000 | 0 |
| Level 1 | H H H L | 0001 | 1 |
| Level 2 | H H L H | 0010 | 2 |
| Level 3 | H H L L | 0011 | 3 |
| Level 4 | H L H H | 0100 | 4 |
| Level 5 | H L H L | 0101 | 5 |
| Level 6 | H L L H | 0110 | 6 |
| Level 7 | H L L L | 0111 | 7 |
| Level 8 | L H H H | 1000 | 8 |
| Level 9 (Full) | L H H L | 1001 | 9 |

## 10. Applications

- Domestic and industrial overhead / underground water tank level monitoring.
- Automatic pump cut-off systems (extendable with a comparator/relay stage).
- Fuel-level or liquid-level indicators in tanks using conductive or resistive liquids.
- Educational demonstration of encoders, decoders, and combinational logic integration.

## 11. Advantages and Limitations

**Advantages**
- Fully combinational design — no microcontroller, firmware, or programming required.
- Simple, low-cost components; easy to prototype on a breadboard.
- Fast, real-time response since there is no software processing delay.
- Good teaching example: combines encoders, inverters, and decoders in one working system.

**Limitations**
- Limited to 10 discrete levels (0–9); finer resolution needs more probes and a wider encoder/decoder chain.
- Direct metal-probe water sensing can corrode probes over time and is affected by water conductivity/purity.
- No memory of levels between readings beyond the CD4511 latch; no logging or remote monitoring without added circuitry.
- Not directly suitable for non-conductive liquids without a different sensing method (e.g., float switches).

## 12. Precautions

- Verify supply voltage is regulated to +5V before connecting the logic ICs; CMOS/HC-series ICs can be damaged by over-voltage.
- Observe correct IC orientation and pin numbering when placing on the breadboard.
- Do not leave unused CD4511 control inputs (LT, BI, LE) floating — tie them to defined logic levels.
- Use insulated/corrosion-resistant probes for actual water contact in a hardware build.
- Keep decoupling capacitors close to the 7805 regulator pins.

## 13. Conclusion

This project successfully demonstrates a working Digital Water Level Indicator built entirely from combinational logic ICs — a 74HC147 priority encoder, a 74HC04 hex inverter, and a CD4511 BCD-to-7-segment decoder/driver — reinforcing key Digital Logic Design concepts such as priority encoding, active-HIGH/active-LOW logic conversion, and BCD-to-seven-segment decoding. The design is simple, low-cost, and easily simulated in Proteus before hardware implementation, making it a suitable and instructive DLD lab project.

## 14. References

- Muhammad Ansar (MArobotic), "Digital Water Level Display using Encoders-BCD to 7Segment-Hex Inverter," YouTube, Jan 17, 2022. https://www.youtube.com/watch?v=u9fR_JzrfQQ
- embeddedlab786, "Digital_Water_Level_Display" (Proteus project files), GitHub. https://github.com/embeddedlab786/Digital_Water_Level_Display
- Texas Instruments, SN54/74LS147 10-Line to 4-Line Priority Encoder Datasheet.
- Texas Instruments, SN5404/SN7404 Hex Inverter Datasheet.
- Texas Instruments / ON Semiconductor, CD4511B BCD-to-7-Segment Latch/Decoder/Driver Datasheet.
