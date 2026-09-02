# ARGUS-1

**AI-Enabled Space Domain Awareness: In-Orbit Demonstration on a 6U CubeSat**

A full mission proposal for an 18-month In-Orbit Demonstration (IOD) of an AI imaging payload for the Space Domain Awareness (SDA) market. From a 550 km sun-synchronous orbit, ARGUS-1 detects, tracks and classifies non-cooperative Resident Space Objects (RSOs), running machine-learning inference on board so only useful detections are downlinked.

Written as my mission design in response to the SpaceYZ AI camera IOD brief, submitted to the Space Faculty CubeSat programme.

📄 **[Read the proposal (PDF)](ARGUS-1.pdf)**

---

## Why this mission exists

The investor case rests on three things one flight has to prove:

1. The AI camera survives and works in low Earth orbit.
2. On-board inference cuts downlink volume by 50 to 100x versus sending raw imagery, which is the economic differentiator for any SDA constellation.
3. A small team can deliver a flight-ready spacecraft on a Series-A budget.

---

## Configuration at a glance

| Item | Selection |
|---|---|
| Platform | 6U bus, deployable solar array, ~28 W orbit-average |
| Payload | 1U AI camera (5 V, 10 W, 1.3 kg) per the brief |
| Compute | GomSpace NanoMind A3200 (always-on bus OBC) + KP Labs Leopard (3 TOPS, power-gated) |
| Pointing | Blue Canyon XACT-15, sub-0.01° |
| Comms | UHF TT&C up/down, X-band payload downlink at ~50 Mbps sustained |
| Orbit | 550 km circular SSO, i = 97.6°, LTAN 10:30 |
| Launch | SpaceX Transporter rideshare, ISISPACE 6U QuadPack deployer |
| Ground | KSAT Lite (primary), AWS Ground Station (backup), SatNOGS (free redundancy) |
| Propulsion | None. Natural decay from 550 km meets the IADC 25-year rule |

## Mission objectives

| ID | Objective | Success criteria | Weight |
|---|---|---|---|
| O1 | On-board AI detection and tracking of RSOs | ≥100 unique RSOs over 6 months at ≥80% precision, detections at ≥1 Hz | 100% |
| O2 | Validate the inference-vs-downlink economics | ≥50x reduction in downlinked data versus raw imagery, catalog accuracy held | +10% |
| O3 | Catalog contribution and sensor characterisation | Feed public catalogs, update the ML model over the air | +10% |

Mission success = 100% of O1. All three reaches 120%.

## The core technical idea

```
Camera (raw frames, 1 Hz)
  -> Leopard FPGA preprocessing (hot-pixel mask, debayer, star mask)
  -> INT8 YOLO inference (boxes + confidence)
  -> multi-frame Kalman tracking
  -> bus computer selects which image chips are worth downlinking
  -> X-band
```

Result: roughly 5 MB of curated data per orbit versus ~500 MB of raw imagery, a 100x reduction.

The two-tier compute split (small FreeRTOS bus computer + power-gated Linux AI processor) follows the pattern flown on Intuition-1, Φsat-2 and HYTI. It isolates safety-critical housekeeping from the AI side and holds idle power under 2 W. Flight software is NASA core Flight System (cFS) with four custom apps: telecommand, payload manager, ADCS bridge, FDIR.

## What is in the proposal

| Section | Contents |
|---|---|
| 1 to 3 | Executive summary, objectives, 3U/6U/12U platform trade study |
| 4 | Subsystem architecture, image-processing pipeline, hardware BoM |
| 5 to 6 | Orbit design and launch strategy, including backup providers |
| 7 to 8 | Team of 16 roles at ~9.9 FTE, lifecycle phases, critical path, 18-month Gantt |
| 9 to 10 | AIT plan (ECSS / NASA GEVS) and three-network ground segment |
| 11 to 13 | Cost plan, top-10 risk register, regulatory and standards compliance |
| Appendix A | Orbit and power derivations |
| Appendix B | Costing basis and ground-ops arithmetic |
| Appendix C | Kerbal Space Program launch rehearsal, including what went wrong |
| Appendix D | ExoAtlas orbit visualisation and 2D ground track |

## Selected numbers

Full derivations are in Appendix A.

| Parameter | Value |
|---|---|
| Orbital period | 95.6 min |
| Orbital velocity | 7.59 km/s |
| Orbits per day | 15.06 |
| Max eclipse | ~35.6 min/orbit |
| Ground-track shift | 23.9° west per orbit |
| Power required (with 30% margin) | 19.6 W against ~28 W provided, ~40% positive margin |
| X-band link | ~50 Mbps sustained, QPSK rate-1/2, 10° elevation |
| Natural decay | ~6 to 8 years |

## On cost, deliberately

The brief asked for a tidy total. A fixed program total cannot be quoted from public prices, because the big-ticket items (AI processor, ADCS, OBC, radios, deployable array, ground passes, test facilities) are all quote-only in this industry.

So the proposal separates the two:

- **Firm, publicly sourced and linked:** S$468,310, covering the ISISPACE 6U structure, the SpaceX rideshare ticket, a 6U mass simulator, a SatNOGS station and the open-source software stack.
- **RFQ register:** every remaining item, listed with a vendor link and marked quote-only, to be issued in Phase 0/A and locked at the purchase order.

Nothing is guessed. That is slower than quoting a round number, but it is the version I can defend line by line.

## Verification approach

- **Kerbal Space Program (Appendix C):** rebuilt the mission and flew a Falcon 9 replica to orbit over roughly 50 attempts, for ascent and insertion intuition. The rehearsal missed the target orbit (747 x 406 km instead of 550 km circular) and the appendix documents that honestly rather than hiding it.
- **ExoAtlas Orbit Visualizer (Appendix D):** modelled the intended 550 km, 97.6° SSO from classical orbital elements, with ECI-frame and 2D ground-track figures.
- **Hand-derived orbital and power maths (Appendix A):** every headline figure in the body traces back to a formula in the appendix so a reviewer can check the working.

## Known limitations

Listed in full in Section 14 of the proposal. The main ones:

- KP Labs Leopard mass and radiation rating are not public and need written vendor confirmation.
- AI detection performance depends heavily on synthetic-training-data fidelity, so expect 30 to 50% recall on the first orbit, improving via on-orbit model updates.
- Vendor inflation has run 8 to 12% per year with lead times stretched to 9 to 14 months, so all prices lock at the purchase order.
- Redundant units for testing and spares are not costed, in order to keep the budget minimal.

## Standards referenced

CubeSat Design Specification Rev 14.1, ECSS-E-ST-10-03C Rev.1, NASA GEVS, IADC 25-year disposal guideline, ITU frequency coordination (IARU for amateur UHF, FCC for commercial X-band), ITAR/EAR jurisdiction review for US and EU flight hardware imported into Singapore.

## Author

**Mihir Sunil Shah**
Year 3 Computer Science undergraduate, minor in Astronomy, National University of Singapore

- Email: mihirsunilshah@gmail.com
- LinkedIn: [linkedin.com/in/mihirsunilshah](https://linkedin.com/in/mihirsunilshah)

## License

This proposal is shared for portfolio and reference purposes. Please credit the author if you cite or reuse any part of it.
