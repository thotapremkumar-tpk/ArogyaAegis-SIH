# ArogyaAegis — SIH 2026 (SIH26181)

**Privacy-first AI health companion vest for heat, pollution and disaster resilience.**
Theme: MedTech / BioTech / HealthTech · Category: Hardware · Team: ArogyaAegis

## Roadmap — three phases

ArogyaAegis is built in three steps on one ESP32-S3 vest. Each phase keeps the wiring of the
one before it, so nothing is thrown away.

| Phase | Scope | Status |
| --- | --- | --- |
| [**1 · Core sensing loop**](phase-1/README.md) | ESP32-S3 + AD8232 chest ECG → on-chip heart rate → DANGER on OLED + vibration; Wi-Fi and Bluetooth dashboards; ECG beat model self-test on the chip. Plus the SIH idea deck and v2 circuit design. | ✅ Completed |
| [**2 · Multi-sensor stress sensing and biofeedback**](phase-2/README.md) | Add GSR, MAX30102 PPG/SpO₂, motion, breathing, a second motor, Li-Po battery and microSD logging. HRV features, WESAD stress model, stress-vs-exercise, paced-breathing haptic feedback, ECG model retrained on our own data. | ⏳ Next |
| [**3 · Heat, air-quality and SOS layer**](phase-3/README.md) | Add skin + air temperature, PM2.5, GPS, SMS SOS, SOS/ACK buttons, buzzer and the v2 power path. Heat-stress, dehydration, AF, fall and respiratory models; graded alerts; auto-SOS; companion app with IMD/CPCB data; field pilot. | ⏳ Planned |

```
Phase 1  ECG ─► ESP32-S3 ─► OLED · motor · Wi-Fi/BLE dashboards                 (done)
Phase 2  + GSR · PPG/SpO₂ · motion · breathing · battery · microSD · stress AI
Phase 3  + skin/air temp · PM2.5 · GPS · SMS SOS · buttons · buzzer · heat/fall/AF AI
```

## Interactive circuit diagram

**Live page:** https://thotapremkumar-tpk.github.io/ArogyaAegis-SIH/

Hover over (or tap) any component to see its inputs, outputs and role in the vest. The pin
map shows which phase wires each pin.

## Repository layout

| Path | What it is |
| --- | --- |
| [`phase-1/`](phase-1/) | Completed work: ECG firmware, Wi-Fi + Bluetooth dashboards, wiring diagrams, ECG model and training code |
| [`phase-2/`](phase-2/) | Plan for multi-sensor stress sensing and biofeedback: parts, pins, models, exit checklist (completed by end of the november)|
| [`phase-3/`](phase-3/) | Plan for the remaining ArogyaAegis features(completed by end of the december) |
| [`docs/index.html`](docs/index.html) | Interactive wiring diagram (served by GitHub Pages) |
| [`docs/app/`](docs/app/) | Bluetooth dashboard, live at https://thotapremkumar-tpk.github.io/ArogyaAegis-SIH/app/ |
| [`phase-2/perfboard/`](phase-2/perfboard/) | Phase 2 + 3 perfboard build guide, live at https://thotapremkumar-tpk.github.io/ArogyaAegis-SIH/phase-2/perfboard/ |
| [`assets/ArogyaAegis_SIH26181.pdf`](assets/ArogyaAegis_SIH26181.pdf) | SIH idea presentation (PDF) |
| [`assets/ArogyaAegis_SIH26181.pptx`](assets/ArogyaAegis_SIH26181.pptx) | SIH idea presentation (editable) |
| [`assets/Arogya_Aegis_Architecture.png`](assets/Arogya_Aegis_Architecture.png) | System architecture |
| [`assets/Arogya_Aegis_Circuit.pdf`](assets/Arogya_Aegis_Circuit.pdf) | Circuit diagram (print version) |

## System overview (all phases)

- **Hardware:** ESP32-S3 (N8R8), AD8232 ECG, GSR, MAX30102 (HR + SpO₂), MAX30205 skin temperature, LSM6DSOX IMU, SHT40, SPS30 PM2.5, L76K GNSS, optional A7670 LTE SMS, microSD, DRV8833 haptics, SSD1306 OLED, Li-Po + BQ24074 + TPS63802.
- **On-device AI (TinyML):** heat-stress (PSI + heat index), dehydration trend, AF/arrhythmia 1D-CNN, stress, fall detection, respiratory risk, personal-baseline autoencoder.
- **Open datasets:** WESAD, PhysioNet/CinC 2017, BIDMC PPG & Respiration, SisFall, Wearable Stress & Exercise (PhysioNet), Air Quality Data in India (Kaggle/CPCB).

Research prototype — not a medical device.
