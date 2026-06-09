# Programmable-gain-photodiode-sensor-with-auto-ranging-network

A precision analog sensor interface featuring a transimpedance amplifier with microcontroller-driven automatic feedback resistor switching. Prevents saturation and amplifies weak signals. Demonstrates mixed-signal circuit design, real-time embedded control, and 2000x dynamic range measurement capability.

## Project Motivation
This project demonstrates core concepts essential for flight control systems:
- Analog signal conditioning and sensor interfacing
- Feedback control loops and auto-ranging algorithms
- Real-time ADC acquisition and noise filtering
- Hardware-software co-design/integration and debugging

 ## Problem Statement
- Photodiode sensors operating across varying light conditions, designed to avoid weak signals or saturation, require either:
(1) **fixed gain**- limited dynamic range, poor resoluion in dim/bright conditions
(2) **Manual gain adjustment**- Requires tedious user intervention, impractical for autonomous systems

  **Solution:** Microcontroller-driven multiplexed feedback network with autonomous gain optimization, enabling continuous operation across varying light conditions.

- ---

## System Architecture

### Block Diagram

[Circuit Block Diagram]<img width="6280" height="2155" alt="Photodiode Signal-2026-06-08-190715" src="https://github.com/user-attachments/assets/6a5aaea9-053e-4c1b-ae8d-e6f9e93b23ca" />

### Component selection: Auto ranging programmable gain network
#### Photodiode SFH203
- Selected for its fast response, high sensitivity, and optimality if spectral wavelength estimation is included in future versions. 
#### OPO7CP Dual supply OPAMP
-Selected for its low noise and wide-ranging supply voltage. Compatible to serve as TIA configuration and buffer. 
#### CD4051BE IC MUX 8:1 240OHM 16DIP
- Selected for wide range of acceptable digital and analog signals. Appropriate dual supply range.
#### 10KΩ, 100KΩ, 1MΩ, 3.3MΩ resistors
- Selected to account for extremely weak signals and oversaturating signals. Allows for wide dynamic range.
#### Arduino nano every microcontroller
- Good for embedded control and many peripherals to utilize or have available for modifications/upgrades.
#### 47pF-100pF feedback transistor
- good balance for smoothing signal at the expense/tradeoff of bandwidth and response time.
  
### system functionality: Auto ranging programmable gain network

The Photodiode (configured in reverse bias) feeds into the inverting input of the Operational amplifier (Op-Amp) which has a trans impedance amplifier (TIA) configuration. The inverting input also receives the feedback transistor and feedback resistors. The common pin of the Mulitiplexer (MUX) is sent to the inverting node of the TIA. The channels 0-3 each respectively hold a resistor value and connect to the output (pin 6) of the TIA. The channel select pins of the MUX (A, B, C respectively) connect to the digital pins 2, 3, 4 of the microcontroller. The non-inverting node of the TIA goes to ground holding both nodes, ideally, at zero. The output of the TIA then feeds into the non-inverting input of a buffer circuit. The buffer circuit is also an Op-Amp but configured as a voltage follower. This is included to reduce too much loading on any part of the circuit. The inverting node has a wire connected to the output. The output of the buffer circuit then feeds into the Arduino Nano Every A0 pin which is an analog input pin. All ground pins are connected for TIA, MUX, buffer, and microcontroller. The TIA, MUX, and buffer are all dual power supply integrated circuits (IC's) and referenced with ± 5 volts. The microcontroller however has been powered by my PC. Firmware written with Arduino's built in library is then uploaded to microcontroller that takes the voltage and converts it into digital steps. Resistor switching uses threshold logic to maintain desireable output voltage. Voltage, feedback resistance, photocurrent, irradiance, and ADC value are all calculated. 

## Data and Results
### Data @ 10KΩ
<img width="428" height="155" alt="Screenshot 2026-06-09 090445" src="https://github.com/user-attachments/assets/708ea4b9-eca4-4cde-9fdd-1bb8faf657af" />
### Data @ 100KΩ
<img width="425" height="227" alt="Screenshot 2026-06-09 090041" src="https://github.com/user-attachments/assets/943580c9-6aa3-4c13-9571-64480db02f9d" />
### Data @ 1MΩ
<img width="428" height="154" alt="Screenshot 2026-06-09 090824" src="https://github.com/user-attachments/assets/7022fe32-2402-4bf8-8049-a8df94d7d39c" />
### Data @ 3.3MΩ
<img width="425" height="228" alt="Screenshot 2026-06-09 090318" src="https://github.com/user-attachments/assets/00be1333-676e-4239-b11f-1c8061fbc950" />






