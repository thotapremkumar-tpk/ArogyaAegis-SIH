# Phase 2 — Complete SereniSuit ⏳ Next

**Status:** planned · **Goal:** finish every feature on the SereniSuit plan, so the suit can
tell **stress from exercise**, **guide the wearer's breathing**, **run all day on a battery** and
**log its own data**. This is the SereniSuit stage of the vest; [Phase 3](../phase-3/README.md)
adds the heat, air-quality and SOS layer that makes it ArogyaAegis.

← [Phase 1 — completed](../phase-1/README.md) · [Roadmap](../README.md#roadmap--three-phases) · Next: [Phase 3](../phase-3/README.md)

Source of scope: the "Planned" rows of the SereniSuit features table, the SereniSuit
architecture ("five sensors in, one brain, four ways out"), and the Phase 2 notes already in
the Demo 1 README.

## Hardware to add

Every new part goes on the pin it keeps in the final v2 board ([`docs/index.html`](../docs/index.html)),
so Phase 3 only adds parts and never re-wires Phase 2.

| Part | Why (SereniSuit feature) | Connects to | Note |
|---|---|---|---|
| GSR / EDA sensor + 2 pads | Sweat response is stress-specific — separates stress from exercise | SIG → **GPIO1** (ADC1) · 3.3 V | Already owned |
| MAX30102 PPG | Backup pulse + SpO₂ | I²C **0x57** on GPIO8/9 · INT → **GPIO11** | Owned. Gave no reliable reading in Demo 1: fix the mount and add LED-current / gain self-calibration |
| MPU6050 accel + gyro | Flags movement so exercise does not raise false alarms; motion-artefact check for ECG/GSR | I²C **0x68** on GPIO8/9 · INT → **GPIO12** | Owned. Replaced by the LSM6DSOX on the same bus and pin in Phase 3 |
| Respiration (flex / chest-stretch sensor) | Breathing rate; guides paced breathing | Analog → **GPIO2** (ADC1, spare in v2) | Owned. Optional in Phase 3, where breathing also comes from ECG + chest IMU |
| 2nd vibration motor (back) | Second haptic zone; distinct calming and alert patterns | DRV8833 BIN1 ← **GPIO13** | DRV8833 already on the board |
| microSD (SPI) | Logs features and short raw windows: personal baseline + our own training data | CS **38** · SCK **39** · MOSI **40** · MISO **41** | Plain logs here; encrypted in Phase 3 |
| 1S Li-Po + TP4056 protected charger + 3.3 V regulator | All-day wear without a power bank | Regulated 3.3 V → 3V3 pin | Li-Po owned. Never a raw battery on the 3.3 V rail |

**Build notes**

- One I²C bus (GPIO8/9) now carries OLED 0x3C, MAX30102 0x57 and MPU6050 0x68 — no address clash. Keep the combined pull-up at 2.2 kΩ or higher: remove extra pull-ups on breakout boards.
- All analog inputs (ECG GPIO5, GSR GPIO1, stretch GPIO2) stay on ADC1, which works with Wi-Fi/BLE on.
- Electrode safety rule from Demo 1 still applies: electrodes on a person only while no charger or laptop cable is plugged in.
- Battery level on the OLED (part of the SereniSuit OLED plan): sensing method still to choose.

## Firmware and ML to add

| Feature | How | Data / evidence |
|---|---|---|
| HRV features | RMSSD, SDNN and LF/HF from the R-R intervals Demo 1 already detects | Kim et al., 2018 — stress lowers HF (parasympathetic) power |
| Signal-quality + motion gating | Per-sensor quality checks; no alert while the MPU6050 says the wearer is moving hard | Oloko-Oba et al., 2026 — false alarms erode trust |
| Stress vs exercise | GSR + HR/HRV + motion: high HR with low motion and rising GSR → stress | — |
| Stress model | 1D-CNN on GSR + ECG/PPG + IMU windows, int8 on the ESP32-S3 | **WESAD** (chest ECG, EDA, respiration, ACC — same body site). Benchmark: 47 ms per 5 s window, F1 89.7 % on WESAD (Jaber, 2026) |
| Wearer calibration | Record a calm baseline before live stress scoring — the reason Demo 1 kept its classifier out of the live loop | Demo 1 Review 1 report |
| Paced-breathing haptic cue | On a stress alert, a slow heartbeat-like vibration on chest + back to slow breathing | Azevedo et al., 2017; Goessl et al., 2017 (HRV biofeedback, g = 0.83) |
| Breathing rate | From the stretch sensor, cross-checked with ECG-derived respiration | BIDMC PPG & Respiration (PhysioNet) |
| SpO₂ | MAX30102 red/IR ratio after self-calibration | Jayathilake et al., 2025 — self-calibrating edge PPG |
| ECG beat model, retrained | Retrain on our own AD8232 beats from the microSD logs; turn on `ENABLE_LIVE_BEAT_SHAPE` only after it passes a held-out-person test | Demo 1 limits: PTB model does not transfer |
| Dashboards | Add GSR, breathing, SpO₂, stress state and battery to the OLED, Wi-Fi page and Bluetooth app | Demo 1 dashboards |

## Done when

- [ ] All five SereniSuit body sensors (ECG, GSR, PPG, motion, breathing) read together on the pins above
- [ ] The suit tells a stress task from exercise in a test with motion gating on
- [ ] Live stress score runs after a calm baseline, and is checked on people not used for training
- [ ] Paced-breathing vibration starts on a stress alert and stops when the wearer recovers
- [ ] Runs a full day on the Li-Po and logs to microSD
- [ ] ECG beat model retrained on our own suit data — or stays switched off
- [ ] Nothing wired in a way Phase 3 has to undo

## Datasets

| Dataset | Used for |
|---|---|
| [WESAD](https://archive.ics.uci.edu/dataset/465/wesad+wearable+stress+and+affect+detection) | Stress model, sensor fusion |
| [BIDMC PPG & Respiration](https://physionet.org/content/bidmc/1.0.0/) | Breathing rate, SpO₂ checks |
| PTB heartbeats (already in Phase 1) + our own AD8232 recordings | ECG beat model retraining |

## References

- Kim et al. (2018), *Psychiatry Investigation* — stress and heart rate variability: a meta-analysis
- Azevedo et al. (2017), *Scientific Reports* — the calming effect of a heartbeat-like wearable vibration
- Goessl et al. (2017), *Psychological Medicine* — HRV biofeedback for stress and anxiety: a meta-analysis
- Schmidt et al. (2018), *ICMI* — WESAD: wearable stress and affect detection
- Jaber (2026), *IJRIAS* — on-wearable stress inference with 8-bit models · [doi:10.51584/ijrias.2026.110200078](https://doi.org/10.51584/ijrias.2026.110200078)
- Jayathilake et al. (2025), *IEEE Sensors Journal* — self-calibrating edge PPG · [doi:10.1109/jsen.2025.3563830](https://doi.org/10.1109/jsen.2025.3563830)
- Oloko-Oba et al. (2026), *Bioengineering* — real-time edge ML for wearable biosignals · [doi:10.3390/bioengineering13050559](https://doi.org/10.3390/bioengineering13050559)
