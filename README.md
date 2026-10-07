# Bipolar Bias-Coil Current Driver — Rev 2

Linear, bipolar current driver for the magnetic bias coils of a cold-atom MOT experiment (Rice University).
One board per axis (X, Y, Z, MOT); four boards in a 19-inch rack.

Please check rev2_doc.pdf for full documentation

![Figure 1](Screenshot%202026-10-07%20132655.png)

## Specifications

| | |
|---|---|
| Load | Series Helmholtz pair, 1.79 mH / 1.31 Ω |
| Output | ±7 A design point (limits +7.7 A / −9.1 A with cool coils) |
| Control | SRS SIM960 analog PID, setpoint from NI DAQ via BNC |
| Feedback | Danisense DP50IP-B fluxgate, 1:250, 100 Ω burden → 0.400 V/A |
| Closed loop | P = 30, I = 2400 s⁻¹: crossover ≈ 1.45 kHz, phase margin ≈ 80° |
| Supplies | Delta ES015-10: ±15 V power pair per board, one ±15 V signal pair shared by four boards |
| Target | Field fluctuation < 1 mG; 10 ppm current stability budget |

## Architecture

OPA455 (gain 1.33) → BD139 V<sub>BE</sub>-multiplier bias spreader with transistor current sink →
MJE15032/33 drivers → 5 × MJL21194G / 5 × MJL21193G complementary emitter followers
(0.22 Ω metal-foil ballasts) → Zobel + L‖R output isolator → coil → DP50IP primary (4 turns) → PGND.

- Separate power (PGND) and signal (SGND) ground domains joined at a single net tie.
- Signal-supply protection: PTC fuse, TVS and reverse-polarity Schottky per rail.
- Status LEDs for all four rails, op-amp fault and sensor fault.

![Figure 2](Screenshot%202026-10-07%20132747.png)

## Hardware

- 4-layer, 1.6 mm, 2 oz copper on all layers, ENIG; 300 × 140 mm.
- All 13 power devices lie tab-up under a clamped heatsink (HeatsinkUSA 8.460″ profile, 177.8 mm long)
  on Sil-Pad 900S; the board is supported from below by a finned aluminium plate.
- Clamp stack: 14 × M3 × 20, 6 mm metal spacers, DIN 6796 spring washers, 1.8 in·lb.

![Figure 3](Screenshot%202026-10-07%20132815.png)

## Verification

Netlist-driven ngspice model with datasheet-validated models for every part, including the supplies,
SIM960 and DP50IP; final runs used the PCB's own connectivity with extracted trace parasitics.

- Bias trim covers zero bias to > 1 A per bank across Q12 h<sub>FE</sub> 100–250 and temperature extremes.
- Stable with all parasitics at worst case (impulse response decays within ~20 µs).
- Steps to ±7 A settle to 0.1 % in < 4 ms; full reversal in ≈ 4.1 ms.
- All components below 52 % of rating.

## Status

Layout complete, DRC clean. Open items: closed-loop noise measurement, coil temperature rise at 7 A
(+7 A requires < ~29 K rise), coil field constant (G/A) to convert the 1 mG requirement to a current specification.
