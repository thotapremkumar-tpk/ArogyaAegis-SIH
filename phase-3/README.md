# Phase 3 — ArogyaAegis: heat, air and disaster layer ⏳ Planned

**Status:** planned · **Goal:** turn the finished SereniSuit into the full ArogyaAegis vest from the
SIH26181 deck — **body + environment in one risk score**, **graded early warnings**, **auto-SOS
with GPS over SMS**, and **offline-first, privacy-first** operation for heat waves, floods and
pollution events.

← [Phase 2 — complete SereniSuit](../phase-2/README.md) · [Roadmap](../README.md#roadmap--three-phases) · Full wiring: [interactive circuit](https://thotapremkumar-tpk.github.io/ArogyaAegis-SIH/)

Everything below is what the SIH deck and the v2 circuit include that Phases 1 and 2 do not.

## 3A · Hardware v2

| Part | Purpose | Connects to |
|---|---|---|
| MAX30205 skin temperature (±0.1 °C) | Armpit reading as a core-temperature proxy for heat stress and fever | I²C 0x48 |
| LSM6DSOX IMU (replaces Phase 2 MPU6050) | Fall detection on its own ML core, activity level, breathing from chest motion | I²C 0x6A · INT1 → GPIO12 |
| SHT40 ambient sensor | Air temperature + humidity → heat index | I²C 0x44 |
| Sensirion SPS30 | PM1 / PM2.5 / PM10 exposure | UART1 GPIO17/18 · 5 V from TPS61023 boost |
| Quectel L76K GNSS + 25 mm patch antenna | Location — shared only during SOS | UART2 GPIO15/16 |
| SIMCom A7670 LTE Cat-1 (optional) + TXS0104E + nano-SIM | SMS SOS without the phone and without mobile data | UART0 GPIO43/44 · PWRKEY GPIO47 |
| SOS button (hold 3 s) · ACK button | Manual alert · cancel a false alarm | GPIO14 · GPIO0 |
| Piezo buzzer via S8050 | Calls nearby people during SOS or after a fall | GPIO21 |
| Li-Po 2500 mAh → BQ24074 → TPS63802 3.3 V | All-day power with charge-while-running power path | PGOOD → GPIO48 |
| Ferrite + LC filter → 3V3_A | Clean supply for ECG and GSR | — |
| MAX17048 fuel gauge | Battery % so SOS is never lost to a flat battery | I²C 0x36 |
| AD8232 SDN moved from 3V3 to GPIO10 | ECG switched off automatically while charging | GPIO10 |
| Textile snap electrodes, breathable mesh vest | Wearable for outdoor work in 45 °C heat | — |

**Changes from Phase 2:** MPU6050 → LSM6DSOX (same bus and interrupt pin) · TP4056 → BQ24074 +
TPS63802 + MAX17048 · stretch sensor becomes optional (breathing from ECG + chest IMU).
Estimated runtime ~26 h at ≈ 80 mA, ~45 h with light sleep — to be measured.

## 3B · On-device AI (TensorFlow Lite Micro + ESP-NN, int8)

| Model | Inputs | Method | Train / validate on |
|---|---|---|---|
| Heat stress | Skin temp, HR, heat index | Physiological Strain Index + heat-index rules, then a small MLP grading 5 levels | [Wearable Stress & Exercise](https://doi.org/10.13026/zzf8-xv61); field pilot |
| Dehydration | HR drift at the same activity, GSR, skin-temp trend | Logistic model | Wearable Stress & Exercise; field pilot |
| Arrhythmia / AF | Single-lead ECG | 1D-CNN, ~50 KB int8 | [PhysioNet/CinC 2017](https://physionet.org/challenge/2017/) |
| Falls | LSM6DSOX | Decision tree on the IMU's ML core, confirmed on the MCU | [SisFall](https://www.mdpi.com/1424-8220/17/1/198) |
| Respiratory risk | Breathing rate (ECG + chest IMU), SpO₂, PM2.5 | Rules | [BIDMC](https://physionet.org/content/bidmc/1.0.0/); [CPCB air quality](https://www.kaggle.com/datasets/rohanrao/air-quality-data-in-india) |
| Personal baseline | All vitals | Autoencoder trained on the wearer's first 7 days; flags deviations | Wearer's own microSD logs |
| Stress | GSR + ECG/PPG + IMU | Carried over from Phase 2 | WESAD |

- **Explainable output:** every alert states its reason, e.g. *"skin 38.4 °C + HR 132 → rest and drink water"*.
- **Multi-sensor agreement:** alert only when 2 or more signals agree, after signal-quality checks.

## 3C · Alerts and SOS

- **Graded alerts** Normal → Pre-warning → Level 1 → 2 → 3, modelled on the HHSEW graded heat-health warning system (Wang et al., 2025).
- **Silent first:** distinct vibration patterns on chest and back, OLED status, buzzer only when needed — no phone required.
- **Auto-SOS:** a fall or an unanswered alert starts a 30 s countdown (ACK cancels), then an SMS with GPS to caregivers / ASHA workers.

## 3D · Companion app and privacy

- Android app over BLE: daily summaries, trends, SOS contacts, regional languages.
- Caches IMD heat-wave warnings and CPCB AQI when online; uses them offline.
- Only alerts and user-approved summaries leave the vest; logs are AES-encrypted on microSD; location only at SOS; clear consent screens; DPDP Act 2023 compliance.

## 3E · Field validation and scale

- Pilot with 30–50 outdoor workers and elderly users in a heat-prone district to collect labelled field data and validate externally — none of the climate-health ML studies reviewed by Ayaz et al. (2026) were externally validated.
- Improve models across vests with federated learning, without collecting raw data (Shokri, 2024).
- Consented, anonymised alerts to district health teams and NDMA/SDMA; flood and cyclone advisories — an open research gap (Ayaz et al., 2026; Ssebyala et al., 2024).
- Run the same models in the phone app on smartwatch and fitness-band data.

## Done when

- [ ] v2 board runs on the full pin map; battery life measured against the 26 h / 45 h estimate
- [ ] Each model is checked on people or data not used for training
- [ ] End-to-end test passes: heat rises → graded alert → no ACK → countdown → SMS with GPS
- [ ] Works with no internet; SOS reaches a phone by SMS without mobile data
- [ ] Pilot completed with consent and a DPDP-compliant data flow

## References

- Wang et al. (2025), *Lancet Regional Health – Western Pacific* — graded heat-health warning (HHSEW) · [doi:10.1016/j.lanwpc.2024.101266](https://doi.org/10.1016/j.lanwpc.2024.101266)
- Moran et al. (1998), *Am. J. Physiology* — the Physiological Strain Index · [PubMed 9688970](https://pubmed.ncbi.nlm.nih.gov/9688970/)
- Kim et al. (2026), *IEEE Trans. Consumer Electronics* — edge neuro-symbolic health analytics, 94.8 % anomaly detection · [doi:10.1109/tce.2025.3646489](https://doi.org/10.1109/tce.2025.3646489)
- Alammar et al. (2026), *IEEE TCE* — federated explainable AI for cardiac alerts · [doi:10.1109/tce.2025.3647850](https://doi.org/10.1109/tce.2025.3647850)
- Subramaniam et al. (2022), *Sustainability* — AI for air-pollution forecasting · [doi:10.3390/su14169951](https://doi.org/10.3390/su14169951)
- Ayaz et al. (2026), *Frontiers in Climate* — climate-health ML review, no flood-health models · [doi:10.3389/fclim.2026.1843933](https://doi.org/10.3389/fclim.2026.1843933)
- Ssebyala et al. (2024), *PLOS Climate* — ML for extreme-weather health risk · [doi:10.1371/journal.pclm.0000338](https://doi.org/10.1371/journal.pclm.0000338)
- Shokri (2024), *Future Generation Computer Systems* — privacy-preserving edge federated learning · [arXiv:2405.05611](https://arxiv.org/abs/2405.05611)
- Nedungadi et al. (2017), *J. Medical Systems* — rural India health monitoring with local languages and health workers · [doi:10.1007/s10916-017-0854-9](https://doi.org/10.1007/s10916-017-0854-9)
