# Tabletop ADCS Demo

A standalone CubeSat-inspired attitude determination and control system (ADCS) bench.

This project is a hardware-and-software demonstrator for spacecraft attitude control concepts using reaction wheels, inertial sensing, embedded control loops, telemetry, fault handling, and simulation-to-hardware validation. The aim is not to build a flight-ready spacecraft subsystem, but to build a rigorous engineering artefact that demonstrates the skills used in small-satellite GNC, embedded systems, verification, and hardware-in-the-loop testing.

## Project write-up

Small spacecraft use attitude determination and control systems to estimate and control their orientation in space. This project recreates a simplified version of that problem on a tabletop platform. A microcontroller reads attitude-rate and attitude-estimate data from an IMU, commands a reaction wheel through a motor driver, and reports telemetry to a host computer for plotting and analysis.

The first milestone is a constrained one-axis reaction-wheel pendulum. This reduces the physics to a manageable control problem while still demonstrating the core spacecraft ideas: attitude sensing, actuator authority, controller tuning, disturbance rejection, saturation management, telemetry, and safe behaviour. Later milestones expand the platform toward a cube or gimballed body that can balance on an edge or corner.

## Engineering goals

- Build a visible, testable ADCS demonstrator rather than a purely simulated controls project.
- Implement a real-time embedded control loop for attitude stabilisation.
- Compare simulated controller performance with measured hardware behaviour.
- Record useful telemetry: angle, angular rate, wheel speed, control effort, mode, and fault state.
- Demonstrate safe mode, actuator limiting, and fault response.
- Produce documentation including diagrams, test results, plots, and demo video.

## Skills demonstrated

- Spacecraft attitude-control concepts
- Control theory: PID first, then optional state-space/LQR
- Attitude estimation using IMU data
- Embedded firmware architecture
- Real-time loop timing
- Motor control and actuator saturation handling
- Telemetry and command protocol design
- Hardware-in-the-loop testing
- Verification, validation, and engineering documentation

## Proposed hardware

Initial MVP hardware:

- Microcontroller board, such as STM32 Nucleo, RP2040, Teensy, or similar
- IMU, ideally with gyro and accelerometer; magnetometer optional for later stages
- DC motor or BLDC motor for reaction wheel
- Motor driver suitable for the selected motor
- Reaction wheel or flywheel with known approximate inertia
- Encoder or tachometer for wheel-speed measurement
- Rigid pendulum arm or single-axis balancing frame
- USB serial telemetry link
- External power supply appropriate for the motor
- Physical guard around the spinning wheel

Optional later hardware:

- 2-axis gimbal or cube-like platform
- Additional reaction wheels
- Magnetorquer coils
- Magnetometer
- Helmholtz coil test setup
- OLED/LCD local status display
- Emergency stop switch

## Safety note

Reaction wheels store kinetic energy. The wheel should be mechanically retained, balanced, guarded, and operated below conservative speed limits. Testing should start at low speed and low controller gain. The software should include explicit wheel-speed limits, motor-current limits where available, and a safe mode that commands zero torque.

## System architecture

```text
+----------------------+       +----------------------+       +--------------------+
| Host PC              | <---> | Microcontroller       | <---> | Motor Driver       |
| - command console    | USB   | - mode manager        | PWM   | - reaction wheel   |
| - telemetry logger   | UART  | - control loop        | DIR   | - wheel encoder    |
| - plotting tools     |       | - estimator           |       +--------------------+
+----------------------+       | - telemetry output    |
                               | - fault manager       |
                               +----------+-----------+
                                          |
                                          | I2C/SPI
                                          v
                               +----------------------+
                               | IMU / sensors         |
                               +----------------------+
```

## Operating modes

| Mode | Purpose |
|---|---|
| `BOOT` | Initialise hardware and perform startup checks |
| `IDLE` | Sensors active, actuator disabled |
| `BALANCE` | Closed-loop reaction-wheel balancing |
| `DETUMBLE` | Rate damping / angular velocity reduction |
| `SAFE` | Actuator disabled or limited after fault |
| `TEST` | Controlled actuator/sensor test mode |

## Fault model

Initial faults:

| Fault | Trigger | Expected response |
|---|---|---|
| `IMU_MISSING` | IMU not detected at startup | enter `SAFE` |
| `IMU_STALE` | sensor data not updated in time | enter `SAFE` |
| `WHEEL_OVERSPEED` | wheel speed exceeds configured limit | cut motor command and enter `SAFE` |
| `LOOP_OVERRUN` | control loop misses deadline repeatedly | enter `SAFE` |
| `HOST_TIMEOUT` | no command heartbeat in armed modes | fall back to `IDLE` or `SAFE` |
| `ESTIMATE_INVALID` | attitude estimate becomes non-finite or out of range | enter `SAFE` |

## Telemetry

Minimum telemetry packet fields:

```text
timestamp_ms
mode
fault
angle_deg
rate_dps
wheel_speed_rpm
control_effort
motor_command
loop_dt_ms
supply_voltage_v
```

Example CSV output:

```csv
timestamp_ms,mode,fault,angle_deg,rate_dps,wheel_speed_rpm,control_effort,motor_command,loop_dt_ms
10240,BALANCE,NONE,4.2,-12.1,1840,-0.42,-88,10
```

## Telecommand interface

Initial command set:

| Command | Description |
|---|---|
| `PING` | Returns an acknowledgement |
| `STATUS` | Prints current mode, fault, and basic telemetry |
| `ARM` | Allows actuator-enabled modes |
| `DISARM` | Disables actuator output |
| `MODE IDLE` | Enters idle mode |
| `MODE BALANCE` | Enters closed-loop balance mode if armed and healthy |
| `MODE DETUMBLE` | Enters rate-damping mode |
| `GAIN SET KP KI KD` | Updates PID gains |
| `GAIN GET` | Prints current gains |
| `FAULT INJECT <name>` | Injects a test fault |
| `FAULT CLEAR` | Clears recoverable faults |
| `LOG START` | Starts telemetry logging |
| `LOG STOP` | Stops telemetry logging |

Command behaviour should be explicit:

```text
ACK <command>
DENY <command> <reason>
REJECT <command> <parse_error>
```

## Roadmap

### Phase 0 — Project definition

Deliverables:

- Final project name
- README
- initial requirements document
- initial block diagram
- selected hardware list
- risk and safety notes
- repository structure

Definition of done:

- The project can be explained in one paragraph.
- The MVP can be built without ambiguous hardware decisions.
- Safety limits are documented before high-speed wheel testing.

### Phase 1 — Simulation baseline

Goal: create a simple one-axis dynamic model before building the physical rig.

Tasks:

- Model pendulum angle, angular rate, wheel torque, and wheel inertia.
- Implement a basic PID controller in simulation.
- Add actuator saturation.
- Plot step response and disturbance rejection.
- Record expected settling time, overshoot, and controller limits.

Definition of done:

- Simulation can stabilise the one-axis model from a small initial angle.
- Saturation behaviour is visible in plots.
- Controller gains are recorded with rationale.

### Phase 2 — Embedded skeleton

Goal: bring up firmware before connecting the reaction wheel.

Tasks:

- Implement startup and heartbeat.
- Implement fixed-frequency control-loop timing.
- Add mode manager.
- Add serial command parser.
- Add telemetry output.
- Add fault manager.
- Add dummy sensor and dummy actuator backends for testing.

Definition of done:

- Firmware runs a deterministic loop.
- Host can send commands and receive telemetry.
- Fault injection works without hardware attached.

### Phase 3 — Sensor bring-up

Goal: acquire reliable attitude-rate data.

Tasks:

- Bring up IMU over I2C or SPI.
- Validate sample rate and timestamping.
- Calibrate gyro bias.
- Implement simple angle estimate.
- Log stationary and moving sensor data.
- Detect stale or invalid sensor data.

Definition of done:

- Sensor data is logged to CSV.
- Gyro bias correction is applied.
- The estimator fails safe when sensor data is unavailable.

### Phase 4 — Reaction wheel bring-up

Goal: safely control wheel speed and torque command.

Tasks:

- Test motor driver at low duty cycle.
- Add wheel-speed measurement.
- Implement wheel-speed limiting.
- Add motor disable path.
- Characterise motor command versus wheel acceleration.
- Add overspeed fault.

Definition of done:

- Wheel can be commanded safely at low speed.
- Wheel speed is measured or estimated.
- Overspeed protection has been tested.

### Phase 5 — 1-axis balance MVP

Goal: stabilise the single-axis rig.

Tasks:

- Mount reaction wheel on pendulum body.
- Tune PID controller conservatively.
- Add arming rules before actuator-enabled modes.
- Demonstrate recovery from small angular displacement.
- Log telemetry during each run.
- Plot angle, rate, wheel speed, and motor command.

Definition of done:

- The rig can stabilise from a repeatable initial condition.
- Telemetry proves closed-loop behaviour.
- At least one disturbance-rejection video is recorded.

### Phase 6 — Engineering polish

Goal: make the project portfolio-grade.

Tasks:

- Add diagrams.
- Add a test report.
- Add plots from successful and failed tests.
- Document controller tuning.
- Document limitations.
- Add demo video and images.
- Add a concise project summary for CV/portfolio use.

Definition of done:

- A reviewer can understand the system from the README alone.
- The repo contains evidence, not just claims.
- The limitations section is honest and technically specific.

### Phase 7 — Stretch goals

Possible extensions:

- Complementary filter or EKF
- State-space controller or LQR
- 2-axis gimbal
- Cube-on-edge balancing
- Cube-on-corner balancing
- Magnetorquer detumble mode
- Hardware-in-the-loop simulation
- Ground dashboard
- Automated test scripts

## Suggested repository structure

```text
tabletop-adcs-demonstrator/
├── README.md
├── LICENSE
├── docs/
│   ├── requirements.md
│   ├── architecture.md
│   ├── control-design.md
│   ├── telemetry-protocol.md
│   ├── test-plan.md
│   └── safety.md
├── firmware/
│   ├── src/
│   └── include/
├── host-tools/
│   ├── telemetry_logger/
│   └── plotting/
├── sim/
│   ├── one_axis_model/
│   └── controller_experiments/
├── hardware/
│   ├── wiring.md
│   ├── bom.md
│   └── cad/
├── test/
│   ├── test_cases.md
│   └── logs/
└── media/
    ├── images/
    └── videos/
```

## MVP acceptance criteria

The project reaches MVP when:

- The embedded firmware runs a fixed-period control loop.
- The IMU is read reliably.
- The reaction wheel is controlled through a safe motor command path.
- The one-axis rig can stabilise from a small initial displacement.
- Telemetry is logged to a host computer.
- At least one successful run is plotted and documented.
- At least three fault cases are demonstrated.
- The README includes architecture, test evidence, and known limitations.

## Test plan summary

| Test ID | Test | Expected result |
|---|---|---|
| ADCS-T001 | Boot without IMU | system enters `SAFE` |
| ADCS-T002 | Boot with IMU | system enters `IDLE` |
| ADCS-T003 | Send invalid command | system returns `REJECT` |
| ADCS-T004 | Enter `BALANCE` while disarmed | system returns `DENY` |
| ADCS-T005 | Arm then enter `BALANCE` | controller activates |
| ADCS-T006 | Simulate stale IMU | system enters `SAFE` |
| ADCS-T007 | Exceed wheel speed limit | motor disabled and fault raised |
| ADCS-T008 | Apply small disturbance | controller rejects disturbance |
| ADCS-T009 | Run for 5 minutes | no loop overruns or unsafe wheel speed |
| ADCS-T010 | Log telemetry | valid CSV generated |

## Portfolio deliverables

- Short demo video
- System block diagram
- Wiring diagram
- Controller design note
- Simulation plots
- Hardware test plots
- Telemetry log examples
- Fault-injection demonstration
- Lessons learned
- CV-ready summary

## CV-ready summary

Built a standalone CubeSat-inspired ADCS bench using an embedded control loop, IMU-based attitude estimation, reaction-wheel actuation, telemetry logging, and fault-handling logic. Developed a one-axis simulation model, validated controller behaviour against hardware tests, and documented architecture, test cases, telemetry, and limitations.

## Current status

Planning stage.

## Licence

TBD.
