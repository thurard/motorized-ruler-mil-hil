# Motorized Flexible Ruler — MIL & HIL

Model-in-the-Loop and Hardware-in-the-Loop study of a motorized flexible beam
("réglet"), from a physical Simscape model through to a **compliant
force-feedback controller** running in real time on the bench.

> SeaTech (Toulon) — 3rd-year engineering project, *Instrumentation & Control* (INCOM), SYSMER track.
> MATLAB · Simulink · Simscape · Simulink Desktop Real-Time · National Instruments PCI-6251.

---

## Overview

A thin aluminium ruler is clamped to the shaft of a DC motor and behaves as a
lightly damped torsional oscillator. The goal was to **model** this flexible
actuation chain, **confront the model with the real bench**, and then **design
control laws** for it — in position, velocity and, finally, in effort
(compliant control).

The work follows a classic model-based design flow:

1. **MIL (Model-in-the-Loop)** — build and validate a numerical model before touching hardware.
2. **HIL (Hardware-in-the-Loop)** — identify the real system and run the controllers against the physical bench.

## The system

| Element | Component | Notes |
|---|---|---|
| Actuator | Maxon **RE025G** DC motor | 24 V, Kt = Ke ≈ 23.5 mN·m/A |
| Reducer | **PL32G** gearbox | ratio N = 5 (bench), N = 50 (acquisition chain) |
| Flexible load | Aluminium ruler | M ≈ 30 g, L ≈ 30 cm |
| Rotation sensor | Incremental **encoder** | 2000 pts/rev, quadrature |
| Effort sensor | Resistive **strain gauge** | bending deformation at the ruler base |
| Acquisition | **NI PCI-6251** | analog I/O + encoder, Simulink Desktop Real-Time |

## What this project covers

- **Simscape model of the ruler** as a torsion spring + viscous damper + equivalent inertia, excited by an ideal torque source.
- **System identification** from the free response using the **logarithmic-decrement** method.
- **Motor + reducer model** parameterised from the datasheet and coupled to the ruler.
- **Open- and closed-loop tests**, oscillation-reduction control and robustness to a perturbation torque.
- **HIL acquisition model**: theoretical vs. experimental block diagrams, and physical-parameter identification on the real bench.
- **Trajectory generation**: a smooth sinusoidal-velocity reference (3 turns each way, 18 s) with input-shaping to avoid exciting the structural mode.
- **Strain-gauge characterisation**: signal conditioning, offset recalibration, sinusoidal and step responses, and identification of the natural bending mode.
- **Compliant force control**: a force-feedback law (effort recalibration, dead-zone, first-order low-pass, gain, saturation) that lets the arm *follow* an operator's push and **actively damps** its vibrations.

## Key results

- Identified modal parameters of the ruler: damping ratio **z ≈ 0.04**, natural frequency **ω₀ ≈ 22.3 rad/s** (pseudo-period **T_d ≈ 0.28 s**, i.e. **f_d ≈ 3.55 Hz**), stiffness **K ≈ 0.45 N·m/rad**.
- The ruler's own natural mode seen on the real strain gauge (**≈ 3.55 Hz**) matches the MIL model — the simulation reproduces the bench's dynamics and damping.
- Motor-side identification: static gain **K ≈ 0.08 rad/(s·V)**, mechanical time constant **τ ≈ 0.015 s**, equivalent inertia **J_eq ≈ 3.5 × 10⁻⁶ kg·m²** (consistent with the datasheet-derived value).
- Open-loop trajectory reaches **6π ≈ 18.85 rad** as specified, and exposes a dead-zone at velocity reversal (dry friction) plus a static drift — motivating closed-loop (PI) control.
- The **compliant controller** was validated experimentally: the motor accompanies a manual effort on the arm while actively attenuating the free vibrations, staying below the dry-friction break-away threshold at rest.

## Repository contents

```
models/
  reglet_physical_model.slx         Simscape model of the flexible ruler (spring–damper–inertia)
  dc_motor_base_example.slx         Reference DC-motor model (starting point)
  1_motor_reducer_reglet.slx        Motor + gearbox + ruler assembly
  2_open_loop_constant_voltage.slx  Open-loop response under constant 24 V
  3_controlled_voltage_input.slx    Controlled-voltage input
  4_trajectory_generation.slx       Sinusoidal-velocity reference (3 turns each way)
  5_compliant_force_control.slx     Compliant force-feedback controller (final)
  archive/                          Intermediate / working versions
scripts/
  motor_parameters.m                Motor & reducer parameters (init script)
data/
  linearization.mat                 Linearisation data
figures/
  simulation_plot.png               Simulation result
report/
  CR1_INCOM_reglet_MIL_HIL.pdf      Full written report (FR)
```

> Build artefacts (`slprj/`, `*.slxc`, `*.autosave`) are not tracked — see `.gitignore`.

## Tools

`MATLAB` · `Simulink` · `Simscape` · `Simulink Desktop Real-Time` · `National Instruments PCI-6251`

## Context

3rd-year engineering project at **SeaTech** (University of Toulon), *Instrumentation
& Control* course, **SYSMER** track (marine & robotic systems).
Team project with **Antoine Hureau** and **Amélie Duchemin** — supervised by **Cédric Anthierens**.
