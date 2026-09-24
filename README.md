# ArogyaAegis — SIH 2026 (SIH26181)

**Privacy-first AI health companion vest for heat, pollution and disaster resilience.**
Theme: MedTech / BioTech / HealthTech · Category: Hardware · Team: ArogyaAegis

## Interactive circuit diagram

**Live page:** https://thotapremkumar-tpk.github.io/ArogyaAegis-SIH/

Hover over (or tap) any component to see its inputs, outputs and role in the vest.

## Files

| File | What it is |
| --- | --- |
| [`docs/index.html`](docs/index.html) | Interactive wiring diagram (served by GitHub Pages) |
| [`assets/ArogyaAegis_SIH26181.pdf`](assets/ArogyaAegis_SIH26181.pdf) | SIH idea presentation (PDF) |
| [`assets/ArogyaAegis_SIH26181.pptx`](assets/ArogyaAegis_SIH26181.pptx) | SIH idea presentation (editable) |
| [`assets/Arogya_Aegis_Architecture.png`](assets/Arogya_Aegis_Architecture.png) | System architecture |
| [`assets/Arogya_Aegis_Circuit.pdf`](assets/Arogya_Aegis_Circuit.pdf) | Circuit diagram (print version) |

## System overview

- **Hardware:** ESP32-S3 (N8R8), AD8232 ECG, GSR, MAX30102 (HR + SpO₂), MAX30205 skin temperature, LSM6DSOX IMU, SHT40, SPS30 PM2.5, L76K GNSS, optional A7670 LTE SMS, microSD, DRV8833 haptics, SSD1306 OLED, Li-Po + BQ24074 + TPS63802.
- **On-device AI (TinyML):** heat-stress (PSI + heat index), dehydration trend, AF/arrhythmia 1D-CNN, stress, fall detection, respiratory risk, personal-baseline autoencoder.
- **Open datasets:** WESAD, PhysioNet/CinC 2017, BIDMC PPG & Respiration, SisFall, Wearable Stress & Exercise (PhysioNet), Air Quality Data in India (Kaggle/CPCB).
