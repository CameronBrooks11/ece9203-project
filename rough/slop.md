# ece9203-project

Course: `ECE9203: Random Signals`

Working Title: `Offline Rocket Flight State Estimation from IMU and Barometric Data Using an Extended Kalman Filter`

## Project Assignment Context

This project will address a random-signals and estimation problem using logged flight data rather than live hardware. The emphasis is on stochastic modeling, sensor-noise handling, state estimation, and quantitative evaluation of estimator performance from noisy measurements, which is well aligned with Kalman-based offline trajectory reconstruction workflows used in aerospace estimation literature.

## Chosen Topic

Design and evaluate an `EKF` that reconstructs the **vertical flight state** of a rocket from logged onboard sensor data.

### Primary estimated states

- altitude `z`
- vertical velocity `v_z`
- accelerometer bias `b_a`

### Primary measurements / inputs

- barometer-derived altitude
- IMU accelerometer
- IMU gyroscope
- IMU magnetometer

The overall idea is consistent with established navigation practice, where EKFs are commonly used to estimate attitude, velocity, position, and IMU biases while using a barometer as a height source.

## Goal

Develop a clear, technically sound, and reproducible estimator that fuses inertial and barometric measurements to reconstruct rocket vertical dynamics offline, then analyze its performance under realistic sensor noise, bias, and modeling assumptions. The DOFPro archive provides real rocket flight data in multiple exported formats for post-flight analysis, making it an appropriate primary reference dataset source.

## Why this scope

This is a better project scope than trying to build a broad “filter zoo” with `KF`, `EKF`, and `UKF` all treated equally. For a course project, a narrower and better-justified estimator is stronger than a shallow comparison of many filters. In practice, `EKF` remains the standard production workhorse in widely used flight-estimation stacks such as PX4, where it estimates quaternion attitude, velocity, position, gyro bias, accelerometer bias, and magnetic-field terms, with barometer support for height estimation.

A `UKF` can still be discussed in the literature review as an advanced nonlinear alternative, and NASA’s 2024 trajectory-reconstruction work is a good example of sigma-point methods being used in offline aerospace estimation when higher-fidelity nonlinear treatment is worth the added complexity.

## Project Objective Statement

Create an offline `EKF`-based state estimator that uses logged IMU and barometer data to estimate rocket altitude and vertical velocity, while explicitly modeling accelerometer bias and measurement/process uncertainty.

## Requirements

### Functional requirements

- ingest and parse offline flight datasets
- preprocess sensor logs into a common time base
- convert pressure to altitude
- estimate orientation or use attitude preprocessing sufficient for vertical acceleration extraction
- propagate the vertical state using inertial measurements
- update the state using barometric altitude measurements
- estimate at least:
  - `z`
  - `v_z`
  - `b_a`
- produce plots and error/consistency diagnostics

### Analysis requirements

- document the state-space model
- justify process-noise and measurement-noise assumptions
- discuss observability and state-choice rationale
- evaluate sensitivity to `Q` and `R`
- compare raw signals against filtered state estimates
- report estimator behavior over key flight phases such as boost, coast, and descent

### Reproducibility requirements

- all core estimation logic implemented in portable `C99`
- `Python` used for data ingestion, experiment control, plotting, and analysis
- `MATLAB` version of the Python scripts will be provided for those who prefer that environment

## Constraints

- no real-time hardware implementation
- no live sensing
- no requirement to fly hardware
- offline-only dataset-driven workflow
- finite course-project scope, so the estimator should remain focused on the vertical channel rather than full 6-DOF navigation
- likely imperfect and heterogeneous rocket data, since public hobby and student rocket datasets are fragmented and vary by logger, format, and sensor completeness. The DOFPro archive explicitly notes multiple native/exported file formats and different avionics/logger sources.

## Main Data Source

Primary offline dataset source:

`https://dofpro.org/RCK/fltdata/FlightData.html`

The DOFPro flight-data archive contains links to multiple real high-power rocket flight data files and supports exported formats such as `.csv`, `.TXT`, `.xtra`, and others, including data from avionics/loggers such as Featherweight Raven, AIM XTRA/Base, and Teensy-based loggers.

## Proposed Technical Approach

## System Architecture

The project is structured as a production-style embedded estimator implemented in `C99`, with offline replay, experimentation, and analysis performed in `Python` (and optionally `MATLAB`).

### 1. Dataset ingestion and replay (`Python`)

- load and parse flight datasets
- normalize units and timestamps
- map dataset fields to estimator inputs
- handle dataset-specific cleanup (formatting, trimming, missing data)

### 2. Estimation core (`C99`)

- pressure → altitude conversion
- vertical acceleration extraction from IMU (including gravity handling)
- EKF state prediction
- EKF measurement update (barometer)
- bias estimation (`b_a`, optional `b_b`)
- covariance propagation
- innovation/residual computation

### 3. Replay execution (`Python`)

- feed time-ordered measurements into the C99 estimator
- log estimated states and diagnostics
- run across multiple datasets

### 4. Analysis and evaluation (`Python` / optional `MATLAB`)

- plot altitude, velocity, bias, residuals, covariance
- compare against:
  - raw barometric altitude
  - vendor-derived estimates
  - GPS (if available)
- evaluate across flight phases and datasets
- perform `Q` / `R` sensitivity analysis

### Nominal state vector

A straightforward state vector is:

`x = [ z, v_z, b_a ]^T`

Possible stretch state:
`x = [ z, v_z, b_a, b_b ]^T`

where:

- `z` = altitude
- `v_z` = vertical velocity
- `b_a` = accelerometer bias
- `b_b` = optional barometer bias

### Why EKF and not only KF

If orientation handling and gravity compensation are treated inside the model, the system is no longer purely linear. That makes `EKF` a reasonable central choice. If orientation is fully preprocessed outside the estimator, then a simpler `KF` could also be used as a baseline, but the main project should stay centered on the `EKF`. Production flight-estimation documentation from PX4 supports this framing by showing that EKF-based estimators commonly include IMU biases and barometric height fusion in a nonlinear navigation setting.

## Suggested Deliverables

- `C99` estimator library
- `Python` scripts for:
  - parsing datasets
  - running experiments
  - plotting and diagnostics
- repository documentation
- final report
- figures showing:
  - raw barometric altitude vs filtered altitude
  - estimated vertical velocity
  - estimated accelerometer bias
  - innovation/residual behavior
  - covariance / confidence trends

## Evaluation Plan

### Minimum evaluation

- compare filtered altitude against raw barometric altitude
- compare filtered vertical velocity against numerical derivatives / baseline approximations
- inspect innovation residuals
- inspect sensitivity to `Q` and `R`

### Better evaluation

- run on multiple flights from DOFPro
- compare behavior across motors / profiles / flight conditions
- quantify robustness to missing or noisy segments

### Stretch evaluation

- compare EKF against a simpler linear KF baseline
- optionally discuss whether a UKF would materially help for stronger nonlinear coupling

## Literature Review / State of the Art

### Core interpretation

The project should position itself as an **offline stochastic state-estimation and reconstruction problem**, not as an embedded-avionics implementation project. That framing is supported by NASA’s trajectory-reconstruction literature, which explicitly treats flight reconstruction as a statistical estimation problem and shows value in more advanced nonlinear filtering for offline aerospace reconstruction.

### State of the art summary

- `Kalman filtering` is the classical foundation for recursive state estimation under stochastic noise assumptions.
- `EKF` remains the standard practical nonlinear estimator in mainstream flight-estimation systems such as PX4.
- `UKF` and other sigma-point methods are more attractive when nonlinearities are stronger and offline accuracy is prioritized over implementation simplicity, as seen in NASA’s 2024 reconstruction work.
- `Random signals` and Kalman-filtering pedagogy are well supported by Brown and Hwang’s text, which is particularly aligned with the course context.
- `Navigation / inertial integration` references such as Groves are relevant because your estimator depends on inertial propagation, gravity handling, frames, and bias/error interpretation.
- `Noise covariance selection` is a major practical issue in Kalman filtering, and modern literature explicitly addresses identification and adaptive approaches for unknown `Q` and `R`.

## OG References and URLs

Use these as the initial canonical stack for the report:

`Kalman (foundational)`
`https://doi.org/10.1115/1.3662552`

`Brown & Hwang — Introduction to Random Signals and Applied Kalman Filtering`
`https://openlibrary.org/books/OL1542504M/Introduction_to_random_signals_and_applied_Kalman_filtering.`
`https://www.scirp.org/reference/referencespapers?referenceid=1755825`

`Särkkä — Bayesian Filtering and Smoothing`
`https://users.aalto.fi/~ssarkka/pub/cup_book_online_20131111.pdf`

`Groves — Principles of GNSS, Inertial, and Multisensor Integrated Navigation Systems`
`https://www.cambridge.org/core/journals/journal-of-navigation/article/principles-of-gnss-inertial-and-multisensor-integrated-navigation-systems-second-editionpaul-d-groves-artech-house-2013-776-pp-isbn13-9781608070053/A3F21EA42F44E0FB063D5D99CA0EDFE6`
`https://www.researchgate.net/publication/224969497_Principles_of_GNSS_Inertial_and_Multisensor_Integrated_Navigation_Systems_Second_Edition`

`PX4 EKF2 documentation`
`https://docs.px4.io/main/en/advanced_config/tuning_the_ecl_ekf.html`

`NASA trajectory reconstruction / sigma-point filtering`
`https://ntrs.nasa.gov/citations/20240001235`

`Noise covariance identification / adaptive Kalman filtering`
`https://pmc.ncbi.nlm.nih.gov/articles/PMC8638515/`

`Primary rocket dataset source`
`https://dofpro.org/RCK/fltdata/FlightData.html`

## Final Project Statement

This project develops an offline `EKF` for reconstructing rocket vertical flight states from logged IMU and barometric data. The primary outputs are altitude, vertical velocity, and accelerometer bias. The implementation will use a portable `C99` estimator core with `Python` for data analysis and visualization, optionally cross-checked in `MATLAB`. The project is scoped as a stochastic estimation and reconstruction problem grounded in canonical Kalman-filter theory, inertial-navigation practice, and real rocket flight datasets.

## Final Project Statement

This project implements a production-style `C99` Extended Kalman Filter for estimating rocket vertical flight states (`z`, `v_z`, `b_a`) from IMU and barometric data. The estimator is executed offline via a Python-based replay and analysis framework using real flight datasets. Evaluation is performed across multiple rockets and sensor configurations, with comparison against onboard estimates and auxiliary references. The focus is on correct stochastic modeling, estimator design, and robustness under real-world data conditions.