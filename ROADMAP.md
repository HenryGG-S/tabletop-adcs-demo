# Roadmap — Tabletop ADCS Demonstrator

## MVP objective

Build a one-axis reaction-wheel attitude-control demonstrator with embedded firmware, IMU sensing, closed-loop stabilisation, serial telemetry, command handling, and documented test evidence.

## Phase breakdown

| Phase | Name | Outcome |
|---|---|---|
| 0 | Definition | Scope, hardware, safety, repo structure |
| 1 | Simulation | One-axis model and baseline controller |
| 2 | Firmware skeleton | Modes, telemetry, commands, faults |
| 3 | Sensor bring-up | IMU acquisition and estimator |
| 4 | Actuator bring-up | Motor driver, wheel speed, safety limits |
| 5 | Balance MVP | Closed-loop one-axis stabilisation |
| 6 | Portfolio polish | Plots, diagrams, video, documentation |
| 7 | Stretch | 2-axis, cube balance, EKF, magnetorquer |

## Suggested issue list

### Phase 0
- [X] Choose final project name.
- [ ] Select microcontroller.
- [ ] Select IMU.
- [ ] Select motor and driver.
- [ ] Define maximum safe wheel speed.
- [ ] Create initial block diagram.
- [ ] Create initial risk register.
- [ ] Create basic repository structure.

### Phase 1
- [ ] Implement one-axis plant model.
- [ ] Implement PID controller.
- [ ] Add actuator saturation.
- [ ] Add disturbance input.
- [ ] Plot simulated response.
- [ ] Save first controller-tuning note.

### Phase 2
- [ ] Implement boot sequence.
- [ ] Implement mode manager.
- [ ] Implement fixed-period loop.
- [ ] Implement serial command parser.
- [ ] Implement telemetry packet.
- [ ] Implement fault state.
- [ ] Add dummy sensor backend.
- [ ] Add dummy actuator backend.

### Phase 3
- [ ] Bring up IMU.
- [ ] Validate sample rate.
- [ ] Estimate gyro bias.
- [ ] Add basic angle estimate.
- [ ] Add stale-data detection.
- [ ] Log stationary data.
- [ ] Log motion data.

### Phase 4
- [ ] Bring up motor driver.
- [ ] Add motor enable/disable.
- [ ] Add wheel speed measurement.
- [ ] Add wheel speed limit.
- [ ] Characterise command versus acceleration.
- [ ] Test emergency stop path.
- [ ] Add overspeed fault.

### Phase 5
- [ ] Build one-axis mechanical rig.
- [ ] Mount wheel and IMU.
- [ ] Run open-loop low-power tests.
- [ ] Run closed-loop low-gain tests.
- [ ] Tune PID controller.
- [ ] Demonstrate disturbance rejection.
- [ ] Save telemetry logs.
- [ ] Produce plots.

### Phase 6
- [ ] Write architecture document.
- [ ] Write control design document.
- [ ] Write telemetry protocol document.
- [ ] Write test report.
- [ ] Add demo images.
- [ ] Add demo video link.
- [ ] Add limitations section.
- [ ] Add CV-ready summary.

## MVP exit criteria

- [ ] One-axis rig stabilises from repeatable small displacement.
- [ ] Telemetry logs include angle, rate, wheel speed, command, mode, and fault.
- [ ] At least one simulation result is compared with hardware data.
- [ ] At least three fault cases are demonstrated.
- [ ] README explains the system without requiring private context.
- [ ] Demo video shows the hardware operating.
