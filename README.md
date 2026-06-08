# Programmable-gain-photodiode-sensor-with-auto-ranging-network

A precision analog sensor interface featuring a transimpedance amplifier with microcontroller-driven automatic feedback resistor switching to prevent saturation and amplify weaker signals. Demonstrates mixed-signal circuit design, real-time embedded control, and 2000x dynamic range measurement capability.

## Project Motivation
This project demonstrates core concepts essential for flight control systems:
- Analog signal conditioning and sensor interfacing
- Feedback control loops and auto-ranging algorithms
- Real-time ADC acquisition and noise filtering
- Hardware-software co-design/integration and debugging

 ## Problem Statement
- Photodiode sensors operating across varying light conditions, designed to avoid weak signals or saturation, require either:
(1) **fixed gain**- limited dynamic range, poor resoluion in dim/bright conditions
(2) **Manual gain adjustment**- Requires tedious user intervention, impracticla for autonomous systems

  **Solution:** Microcontroller-driven multiplexed feedback network with autonomous gain optimization, enabling continuous operation across varying light conditions.

- ---

## System Architecture

### Block Diagram

[Circuit Block Diagram]<img width="6280" height="2155" alt="Photodiode Signal-2026-06-08-190715" src="https://github.com/user-attachments/assets/6a5aaea9-053e-4c1b-ae8d-e6f9e93b23ca" />

### Design approach: programmable gain network

