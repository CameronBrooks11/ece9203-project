# Project Proposal

**Course:** ECE 9203 / ECE 9023 — Random Signals, Winter 2026
**Title:** Offline Rocket Flight State Estimation from IMU and Barometric Data Using an Extended Kalman Filter
**Topic Area:** Kalman filter applications / nonlinear state estimation

---

## Objective

Design, implement, and evaluate an offline Extended Kalman Filter (EKF) that reconstructs the vertical flight state of a high-power rocket from logged onboard IMU and barometric sensor data. The project is framed as a stochastic estimation and reconstruction problem, not an embedded-avionics implementation.

---

## Scope and Justification

A narrowly scoped, well-justified EKF targeting the vertical channel is stronger than a shallow multi-filter comparison. The EKF is the standard production nonlinear estimator in flight-estimation stacks (e.g., PX4 EKF2) and directly applies course material on Kalman filtering, stochastic modeling, and sensor-noise treatment. A UKF will be discussed in the literature review as a higher-fidelity alternative, supported by NASA's 2024 offline trajectory-reconstruction work.

---

## State Vector and System Model

**Primary state vector:**

$$\mathbf{x} = \begin{bmatrix} z & v_z & b_a \end{bmatrix}^T$$

| Symbol | Description        |
| ------ | ------------------ |
| $z$    | Vertical altitude  |
| $v_z$  | Vertical velocity  |
| $b_a$  | Accelerometer bias |

**Stretch state:** augment with barometer bias $b_b$.

**Measurement input:** barometric altitude (converted from raw pressure).
**Inertial input:** accelerometer along the vertical axis (gravity-compensated, bias-corrected).

The system is nonlinear when orientation handling and gravity compensation are included inside the estimator model, motivating EKF over a plain linear KF. A linear KF baseline will be included for comparison if orientation is fully pre-processed externally.

---

## Technical Approach

### Architecture

| Layer                   | Language                   | Responsibility                                                                                                                       |
| ----------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Data ingestion & replay | Python                     | Parse/normalize datasets, feed time-ordered measurements to estimator                                                                |
| Estimation core         | C99                        | Pressure→altitude, vertical acceleration extraction, EKF predict/update, bias estimation, covariance propagation, innovation logging |
| Analysis & evaluation   | Python (+ optional MATLAB) | Plotting, diagnostics, Q/R sensitivity analysis, cross-dataset comparison                                                            |

### EKF Steps (C99 core)

1. **Predict** — propagate state and covariance using IMU measurements and process noise `Q`
2. **Update** — fuse barometric altitude measurement with noise `R`
3. **Output** — estimated `z`, `v_z`, `b_a`; innovation residuals; covariance traces

---

## Datasets

All data sourced from the DOFPro flight-data archive: <https://dofpro.org/RCK/fltdata/FlightData.html>

| Role                | Rocket                                 | Logger Types                    | Purpose                               |
| ------------------- | -------------------------------------- | ------------------------------- | ------------------------------------- |
| Primary development | 38 mm Prototype (J510W / J570W / J150) | Raven, XTRA, onboard, emulation | Core implementation and main results  |
| Validation          | Madcow Adventurer                      | Raven + GPS                     | Cross-rocket generalization           |
| Robustness          | LOC Precision Vulcanite                | Trimmed logs + GPS              | Estimator stability on imperfect data |

No onboard system is treated as ground truth. References are raw barometric altitude, vendor-reported estimates, and GPS altitude where available — all treated as imperfect measurements.

---

## Evaluation Plan

| Level   | Approach                                                                                                          |
| ------- | ----------------------------------------------------------------------------------------------------------------- |
| Minimum | Filtered altitude vs. raw baro; filtered velocity vs. numerical derivative; innovation residuals; Q/R sensitivity |
| Better  | Multi-flight comparison across motors/profiles; robustness on noisy/missing segments                              |
| Stretch | EKF vs. linear KF baseline; UKF discussion                                                                        |

Key diagnostics: innovation sequence consistency, covariance bounds, bias convergence, behavior across boost / coast / descent phases.

---

## Deliverables

- `C99` EKF estimator library (portable, no dependencies)
- Python scripts: dataset parsing, replay, plotting, diagnostics
- Optional MATLAB equivalents of Python scripts
- Final report covering: stochastic model derivation, observability analysis, noise covariance justification, results figures, literature review
- Key figures: raw baro vs. filtered altitude, estimated velocity, accelerometer bias, innovation/residual behavior, covariance trends

---

## Constraints

- Offline-only, dataset-driven workflow — no live hardware or real-time implementation
- Vertical channel only — no full 6-DOF navigation
- Heterogeneous public datasets — varying logger formats, sensor completeness, and data quality expected

---

## Key References

| Reference                                                                                                             | Notes                                      |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| Kalman (1960) — [doi:10.1115/1.3662552](https://doi.org/10.1115/1.3662552)                                            | Foundational                               |
| Brown & Hwang — _Introduction to Random Signals and Applied Kalman Filtering_                                         | Primary course-aligned text                |
| Särkkä — _Bayesian Filtering and Smoothing_ ([PDF](https://users.aalto.fi/~ssarkka/pub/cup_book_online_20131111.pdf)) | Theoretical foundation                     |
| Groves — _Principles of GNSS, Inertial, and Multisensor Integrated Navigation Systems_                                | Inertial navigation and bias modeling      |
| PX4 EKF2 documentation — [px4.io](https://docs.px4.io/main/en/advanced_config/tuning_the_ecl_ekf.html)                | Production EKF reference                   |
| NASA NTRS 20240001235 — trajectory reconstruction                                                                     | Offline sigma-point filtering in aerospace |
| Noise covariance identification — [PMC8638515](https://pmc.ncbi.nlm.nih.gov/articles/PMC8638515/)                     | Adaptive Q/R selection                     |
