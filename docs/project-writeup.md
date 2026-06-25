# Project Write-up — Tabletop ADCS Demonstrator

## Summary

The Tabletop ADCS Demonstrator is a standalone CubeSat-inspired attitude determination and control bench. It uses a reaction wheel, inertial sensing, embedded control software, telemetry, and fault handling to reproduce a simplified spacecraft attitude-control problem on physical hardware.

The first target is a one-axis reaction-wheel pendulum. This provides a constrained but meaningful platform for testing estimation, control, actuator limits, disturbance rejection, and real-time embedded behaviour. Later extensions may include multi-axis balancing, cube-on-edge or cube-on-corner balancing, magnetorquer detumbling, and hardware-in-the-loop simulation.

## Motivation

A project aimed at the space industry should demonstrate more than general electronics or robotics ability. It should show evidence of spacecraft-relevant thinking: modes, faults, telemetry, actuator constraints, validation data, and clear documentation. This demonstrator is designed around those signals.

## What makes it portfolio-worthy

- It is physically demonstrable.
- It maps naturally to spacecraft ADCS concepts.
- It produces measurable test data.
- It requires both theory and practical engineering.
- It can be documented like a small spacecraft subsystem.
- It leaves room for advanced extensions without depending on them for success.

## Intended final demonstration

A video and written test report showing the rig stabilising itself from a small angular displacement, rejecting a disturbance, logging telemetry, and entering safe mode when a fault is injected.

## Scope boundaries

This is not a flight-ready ADCS. It is a tabletop engineering demonstrator. The aim is to prove competence in modelling, embedded control, system architecture, testing, and documentation.
