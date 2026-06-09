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

## Process & debugging
### Phase 1: Initial set up
**Initial Challenge: Arduino IDE Configuration**
- Downloaded latest Arduino IDE
- Installed Mega board package for Nano Every
- LED blink test appeared to fail
- Troubleshooting steps attempted:
  - Tested different delay times
  - Verified serial communications
  - Uninstalled/reinstalled board packages
  - Tried earlier IDE versions
  - Pressed reset button on microcontroller
  - Attempted "burn bootloader" (online suggestion)

**Root Cause:** The error was an automatic IDE message, not an actual fault. This was actually a valuable lesson in distinguishing real failures from false alarms during embedded systems debugging.

**Learning:** Environmental issues are often the first thing to investigate, but also the easiest to misdiagnose without careful verification.

### Phase 2: component level testing
**Strategy:** Test each subsystem in isolation before integration.
#### Photodiode Testing (2/26)
Component: SFH 203 Photodiode Test Setup: 33.67 kΩ load resistor (nominal 33k, measured 32.67k) Cathode: +5V, Anode: → 33kΩ → GND Light Source: iPhone 15 Pro Max camera light

Results: No light: <70 mV Close (3"): 300-400 mV Medium (6"): 200-300 mV Far (12"): 70-130 mV

Conclusion: ✓ Photodiode functional across distance range Evidence: Video documentation saved

#### Op-Amp Biasing Issue (2/26)

**Problem Discovered:** OP07CP is not rail-to-rail or single-supply compatible
Initial Setup: 0V to +5V single supply Observed Issue: Inverting input floating at ~1.2V (not at virtual ground) Impact: TIA transimpedance formula broken V_out = I_photo × R_f no longer valid
**Solution Implemented:**
Changed to: Dual supply ±3.3V (total 6.6V range) Result: Inverting input now correctly at 0V virtual ground Outcome: ✓ TIA operation restored, proper current-to-voltage conversion

**Learning:** Op-amp supply requirements are critical. Single-supply op-amps require specific input/output range considerations. Always check datasheet rail-to-rail specifications.

#### Feedback Stabilization (2/26)
Problem: Oscillation in TIA output Solution: 10pF capacitor in parallel with 3.3MΩ feedback resistor (Measured actual: 3.27 MΩ) Result: Stable output across gain settings
### Phase 3: Environmental & Measurement Conditions (2/16)

**Controlled Light Environment:**
- Closed curtains and covered windows
- Reduced ambient light interference
- More consistent, repeatable measurements

**Calibration Data (2/16):**
3.3M Resistor: Dark room: 143.3 mV → 44 nA photocurrent iPhone light: 3.2V (3-5" away) → ~975 nA photocurrent

10K Resistor: Dark room: 0.4 mV → 40 nA photocurrent iPhone light: 354 mV (3-5" away) → ~35.4 µA photocurrent
**Note:** Measurements at uncontrolled distances; proportional analysis not valid.

---

### Phase 4: Multiplexer & Microcontroller Integration Delay

**Waiting for Components:**
- MUX not yet arrived from supplier (incorrect part sent)
- Decision: Integrate microcontroller with single fixed resistor
- Use LEDs as visual feedback for threshold logic

**Buffer Stage Added (2/16):**
TIA Output: 205 mV Buffer Output: 195 mV (voltage follower, minimal loss) Purpose: Isolate TIA from ADC input impedance

---

### Phase 5: Microcontroller & GPIO Testing (2/18-2/19)

#### GPIO LED Control
Objective: Verify microcontroller GPIO before integrating sensor Test Setup: External LED + 680Ω current-limiting resistor (used 3× 680Ω in parallel for lower resistance) Result: ✓ GPIO control functional

#### Critical Discovery (2/19)
Problem: GPIO control code not working despite correct logic Troubleshooting: Verified pin numbering (D2 vs PD2), code logic Root Cause: Microcontroller board not fully seated in breadboard Solution: Pressed board down until fully inserted Result: ✓ All GPIO control now working

**Learning:** Simple physical issues can masquerade as software bugs. Always verify hardware connectivity before debugging code.

---

### Phase 6: ADC Integration & Threshold Logic (2/19)

#### ADC Conversion Formula
Discovery: 5V / 1023 provides adequate resolution Upgrade: Changed to 4.096V / 1023 for better granularity Rationale: More ADC steps allow finer threshold discrimination

Formula: Voltage = (ADC_value × V_ref) / 1024 Voltage = (ADC_value × 4.096) / 1023

#### LED Threshold Testing
ADC Readings (Room Conditions): Covered (dark): ~11 ADC counts Normal room light: ~70 ADC counts Direct light: ~700 ADC counts

LED Response: Expected ON: Threshold set to 2V Actual ON: ~0.6-0.9V Adjusted: Set threshold to 2V to avoid false triggers

#### Critical GPIO Bug (2/19)
Problem: LED would not turn on despite correct ADC readings Serial Debug: Added Serial.print() to monitor ADC values Confirmed voltage signal reaching ADC (700 counts with light) Root Cause: LED accidentally removed from ground connection Solution: Reconnected LED to ground Result: ✓ LED threshold logic now functional

**Learning:** When hardware "works in pieces but not together," check mechanical connections before code. Multimeter + Serial.print() are essential debugging tools.

---

### Phase 7: Fixed Resistor Testing (2/24)

#### Voltage Calibration
Power Supply: Vcc = +5.05V, GND = -5.1V (allowing for voltage drop measurements)

3.3M Resistor Maximum Gain Test: iPhone light input: 4.48V output (approaching saturation)

Threshold Mapping: ADC 358: Voltage = 1.75V (OFF threshold) ADC 410: Voltage = 2.009V (ON threshold)

---

### Phase 8: Multiplexer Verification (2/27-2/28)

#### MUX Functionality Check
Test Method: Ground and +5V on select inputs Serial.print() to verify addressing Result: ✓ MUX control working correctly

#### Auto-Ranging Code Structure
Headers Created:

resistor_switching.h/.cpp (MUX control)
adc.h/.cpp (ADC reading/averaging)
sensor_state.h/.cpp (data logging)
debug.h/.cpp (diagnostic output)
Thresholds Set: Upper: 4.0V (prevent saturation) Lower: 0.9V (prevent noise-triggered switching)

---

### Phase 9: Breadboard Leakage Current Issues (3/1-3/2)

#### Unexpected Behavior
Initial Observation: Output stuck at 3.5V regardless of resistor Inverting input: ~150mV Non-inverting input: ~0V

Diagnosis Process:

Removed MUX → Still broken
Removed feedback resistors → Output went to 0V
Removed photodiode → Inputs/outputs at 0V
Reintroduced photodiode only → ✓ Worked correctly
Added 10K feedback resistor → ✓ Still worked
Added larger resistors (MΩ) → ✗ Broke again
Root Cause: Breadboard leakage current with high-impedance components (>MΩ) caused parasitic currents to dominate signal current Op-amp responded to leakage, not photocurrent

Solution: Temporarily use lower resistance values on breadboard Defer high-impedance testing to PCB PCB layout will eliminate leakage paths

**Learning:** Breadboard is acceptable for prototyping but has limitations. High-impedance circuits (>1MΩ) are susceptible to leakage current and parasitic effects. PCB design is essential for production.

---

### Phase 10: System Integration & Auto-Ranging Refinement (3/1-3/5)

#### First Integration Success (3/1)
Serial Print Rate: Initially too fast to monitor Solution: Added rate limiting via millis() if (millis() - lastPrint > 500) { ... } Result: Readable output every ~500ms

#### Rapid Switching Problem (3/1-3/5)

**Initial Issue:**
With 3M resistor in bright light: Voltage exceeds 3.8V → Switch to 1M Voltage drops below threshold → Switch back to 3M Result: Rapid oscillation between resistors

**Attempted Solution #1:** Require multiple threshold crossings
Change counter: 2, then 4 consecutive readings Result: Still oscillated, just slower

**Final Solution (3/5):** Hysteresis-based ranges
Instead of single upper/lower threshold:

State: 3M (highest gain) if V > 3.8V → switch to 1M else → stay at 3M

State: 1M if V > 3.8V → switch to 100K else if V < 0.5V → switch to 3M else → stay at 1M

State: 100K if V > 3.8V → switch to 10K else if V < 0.5V → switch to 1M else → stay at 100K

State: 10K (lowest gain) if V < 0.5V → switch to 100K else → stay at 10K

Result: ✓ Smooth transitions, no oscillation

---

### Phase 11: Data Structure & Sensor State (3/16)

#### Created Sensor Data Struct
```cpp
struct sensorData {
  float Voltage;              // Raw ADC voltage
  int feedback_res;           // Active resistor value
  float photocurrent;         // Calculated I_photo
  float irradiance;           // Calculated intensity
  int rawADC;                 // 10-bit ADC reading
};
Code Organization
Modular functions for each subsystem
Serial logging of all sensor values
Real-time resistor state tracking
Phase 12: PCB Design (3/27-3/30)
Initial Challenges
Code
Component Placement:  Multiple board reorganizations
                      Difficult to route without overlaps
Footprint Error:      Ordered wrong component footprint
                      Corrected and re-routed entire board
PCB Improvements Implemented
Created complete ground plane (improved current paths)
Added vias for layer transitions
Corrected op-amp footprints and pin configurations
Reduced routing time by 10× after ground plane implementation
Ignored non-critical silkscreen warnings
Future Improvements (v2)
Switch from surface-mount to through-hole resistors/capacitors
Better component orientation for soldering
More rigorous layout review before fabrication


## Skills Demonstrated
- Analog circuit design (transimpedance amplifier)
- Microcontroller programming (Arduino)
- Auto-ranging feedback control
- ADC interfacing and signal conditioning
- Mixed-signal debugging
- Hardware-software integration
- UART communication
- Problem-solving under constraints

## Future Improvements





