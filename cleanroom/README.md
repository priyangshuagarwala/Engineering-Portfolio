# Cleanroom Safety Alarm System

**STM32 environmental-monitoring and alarm prototype · Completed project**

A hands-on embedded project exploring how temperature, humidity and airborne-particle sensor signals can be collected and displayed, with an audible-warning output. The project focuses on bare-metal C, sensor interfacing and practical hardware debugging.

> Educational prototype—not a certified safety system, calibrated particle counter, or proof that a cleanroom is safe. Do not use it for life-safety decisions or cleanroom certification.

## Completion and documentation scope

**Project completed, as confirmed by the project author on 25 October 2026.** The development notes below describe the build process and earlier debugging, not a list of current project failures. Final firmware, updated wiring documentation and final test records are not included here, so this update does not add calibration, accuracy, certification or PCB-fabrication claims.

## Project overview

The project brings together an STM32F411CEUx, an SHT3x temperature/humidity sensor, a Sharp GP2Y1010AU0F optical dust sensor and an I²C LCD. The earlier development record includes a separate transistor-driven buzzer test. An MQ-2 gas-sensor path was explored earlier but was set aside during integration.

```text
SHT3x temperature / humidity ── I²C ───────┐
                                         │
GP2Y1010 optical sensor ── divider / ADC ──┤ STM32F411CEUx ── I²C LCD
                          LED timing ◀───┤       │
                                         │       └── UART diagnostic output
                                         └── Buzzer driver (separately tested)
```

## System operating concept

The system has two different kinds of input: a digital temperature/humidity measurement and an analog optical-sensor signal. Keeping these paths separate is important because the values do not mean the same thing.

1. **Initialize the controller and interfaces.** Firmware configures the selected GPIO pins, timing functions, ADC and communication paths. The pin definitions must match the connected hardware and the particular firmware version being tested.
2. **Read temperature and humidity.** The SHT3x path exchanges digital data over I²C. The firmware converts the returned data into temperature and relative humidity for display. Changing values were observed in serial logs, but comparison against a reference instrument has not been documented.
3. **Acquire the dust-sensor signal.** The GP2Y path controls its LED and samples the analog output at a defined point in the measurement cycle. The output divider sits between the sensor and the STM32 ADC input. This timing-sensitive path was a significant integration challenge during development.
4. **Format the measurements.** Temperature and humidity can be displayed with their respective units. The archived dust diagnostics report raw ADC counts and estimated voltage; they do not establish calibrated particle concentration. The diagnostics distinguish `D`, the ADC count, from `Vo`, an estimate of the sensor output voltage.
5. **Display and diagnose.** The LCD provides a local interface. UART output provides a second view of readings, loop progress and errors when debugging. A working UART stream can help establish that the program is running even when the LCD is blank or reporting a communication failure.
6. **Drive the alarm output.** The alarm stage uses sensor-based decisions to command the buzzer. Earlier notes documented standalone buzzer testing before integration; the final thresholds and fault-response implementation are not specified in this repository.

### Why each block is present

- **STM32:** coordinates acquisition, timing, formatting and output. Register-level programming makes peripheral configuration an explicit part of the project.
- **SHT3x:** provides the temperature/humidity path. It does not validate particle cleanliness or establish optical-sensor calibration.
- **GP2Y optical sensor:** provides a particle-sensitive analog signal. Its readings need stable acquisition and calibration before a concentration claim is meaningful.
- **LCD:** makes the prototype usable without keeping a serial terminal open. It is a display, not independent confirmation that a measurement is correct.
- **UART diagnostics:** expose internal behavior that may not fit on the LCD, including loop counters and error counters.
- **Buzzer driver:** provides a separately powered audible-output stage under GPIO control. A working buzzer proves an output test, not successful environmental alarm detection.

## Hardware and development tools

| Item | Role |
|---|---|
| STM32F411CEUx | Main microcontroller; register-level firmware |
| SHT3x | Temperature and relative-humidity measurements |
| Sharp GP2Y1010AU0F | Optical particle-sensor signal acquisition |
| LCD with I²C backpack | Local readings and diagnostic display |
| TMB12A05 buzzer and BC547B transistor | Audible-output bench test |
| ST-LINK, STM32CubeIDE and STM32CubeProgrammer | Build, programming and debugging workflow |
| USB-to-UART adapter and Tera Term 5 | Serial diagnostics |
| Digital multimeter and breadboard | Supply, wiring and signal checks |

### Recorded development pin allocation

This table preserves the development integration plan. It is not a newly confirmed final pin map or a fabrication-ready schematic.

| Function | STM32 pin(s) |
|---|---|
| LCD SCL / SDA | PB6 / PB7 |
| SHT3x SCL / SDA | PB8 / PB9 |
| GP2Y LED timing control | PB0 |
| GP2Y analog input | PA1, through an output divider |
| Buzzer control in the integration plan | PB1 |
| UART transmit | PA2 |
| SWD programming / debugging | PA13 / PA14 |

Standalone tests reused some pins. In particular, the buzzer was tested on PB0 before PB0 was reserved for the dust sensor. The final circuit and firmware must use a consistent, conflict-free assignment.

### Power and wiring considerations

The development wiring involves both a 5 V supply domain and 3.3 V controller/sensor interfaces. The recorded integration plan includes a shared ground, a divider on the GP2Y analog output, and a resistor/capacitor network for its pulsed LED supply. The recorded plan also called for a bidirectional level-shifting interface for the LCD backpack.

These are **design considerations to verify**, not a validated assembly guide. Exact breakout-board circuitry, supply requirements, pull-ups, transistor pinout and power sequencing must be checked against the actual parts before wiring or PCB fabrication. Earlier development advice changed between test versions; it should not be combined into a final schematic without review.

Particular review points are:

- Keep the sensor's supply connection distinct from its analog output. The ADC divider receives the sensor signal, not an arbitrary supply rail.
- Check the controller-side analog voltage over the intended operating range, including tolerances and fault conditions. A divider alone is not evidence that the complete input is protected.
- Verify the GP2Y LED-control circuit and firmware polarity together. A circuit change can change which GPIO state turns the LED on.
- Confirm that I²C pull-ups and interface voltages match the devices on each connection.
- Do not combine independent power outputs casually. Establish one documented power arrangement and preserve the required ground reference.
- Reserve programming pins and resolve reused GPIO assignments before connecting all modules.
- Inspect wiring with power removed, then use controlled bench checks rather than assuming a breadboard rail or jumper is continuous.

## Firmware approach

- Bare-metal C with direct peripheral-register access rather than HAL calls.
- ADC acquisition and raw-count/voltage diagnostics for analog sensor paths.
- Timed LED control and ADC sampling for the GP2Y sensor.
- I²C communication for the environmental sensor and LCD.
- UART debug output with a measurement-loop counter and peripheral error counters in diagnostic firmware.
- Separate peripheral tests before combined integration.

These features span successive development versions; they are not a claim that one final firmware image has passed complete system testing.

### Firmware responsibilities during development

The following preserves the functional breakdown and evidence available during development. Historical integration gaps below do not override the completed project status; final implementation details are not yet published.

| Functional area | Responsibility and status |
|---|---|
| Clock and timing setup | Establishes the time base used by serial communication and sensor timing; must be verified for the selected build. |
| GPIO configuration | Assigns input, output and communication behavior; needs to stay synchronized with the wiring map. |
| SHT acquisition | Requests and processes temperature/humidity data. Development code descriptions include CRC checking; the recorded logs show readings, not an independent audit of every error path. |
| ADC acquisition | Starts conversions and obtains raw counts. Earlier MQ-2 diagnostic code included averaging; that does not establish stable GP2Y measurements. |
| GP2Y timing | Coordinates LED control and analog sampling. A sampling-setting correction was proposed during troubleshooting, but later development reports still showed unstable values. |
| LCD formatting | Presents readings and status through the backpack interface. Display activity and communication failures were both reported at different stages. |
| UART diagnostics | Reports values, loop sequence numbers and error counters in diagnostic versions. UART is optional for ordinary local display but useful during validation. |
| Alarm decision logic | The development plan covered thresholds, hysteresis, fault response and sensor-driven buzzer behavior; final settings and test records are not included here. |

A loop counter shows that execution is progressing; it does not prove that every sensor returned a valid new measurement. Similarly, a zero error counter means that a particular check did not report an error during that run—it does not establish calibration, accuracy or complete fault coverage.

## Development results and evidence

**Observed during development**

- Serial logs show changing SHT3x temperature and humidity readings and an advancing measurement-loop counter. One recorded run showed approximately 23.6–23.8 °C and 77–79% RH; these are bench observations, not accuracy specifications.
- The LCD displayed numerical readings during testing, although LCD communication faults also occurred in some firmware/wiring configurations.
- The standalone buzzer circuit was reported working after breadboard and transistor-connection troubleshooting.
- Build, programming, power-rail and sensor-interface problems were investigated using compiler output, UART logs and multimeter measurements.

**Historical issues and limits of the archived evidence**

- GP2Y readings fluctuated substantially in earlier development tests, including raw ADC values reported between 0 and 1700. These observations record a debugging stage, not the current completed-project status; no final calibration dataset is included.
- Raw ADC counts and estimated sensor voltage are not calibrated dust concentration or gas ppm.
- The archived development record does not specify final end-to-end alarm tests, thresholds, hysteresis or fault-handling results.
- A combined pin map was prepared for PCB planning; PCB fabrication and validation are not established.

## Development history and validation approach

The development record is useful because it captures problems encountered while bringing real hardware together, not just an ideal block diagram.

### Issues investigated

- **Wrong MCU header:** an early build targeted STM32F411CEUx while the supplied example referenced an F401 header. This exposed the need to align code, target selection and device support.
- **Display versus acquisition:** UART logs showed advancing loops and changing SHT values while the LCD reported errors. That separated a display-link problem from a completely stopped application.
- **Breadboard versus GPIO output:** the user observed switching directly at the MCU pin but not through a breadboard connection during buzzer testing. This narrowed the investigation to the connection and driver path.
- **Unstable analog readings:** grounding checks, supply measurements, divider questions and timing changes were explored. These historical troubleshooting notes do not establish a final calibration result.

### Validation checklist used to organize the development approach

| Stage | What to check | Evidence to retain before moving on |
|---|---|---|
| Reproducible build | Exact MCU target, complete source, selected clock assumptions and clean build | Source revision, tool versions and build log |
| Programming and startup | Correct image is programmed and executes after reset | Programming verification and identifiable startup output |
| Power and connectivity | Supply rails, shared ground, continuity and interface voltages | Annotated schematic and measured test points |
| ADC baseline | Known, appropriate test inputs with sensors isolated | Raw-count logs and simultaneous reference measurements |
| SHT acquisition | Repeatability and agreement with a suitable reference | Timestamped measurements with reference conditions |
| GP2Y timing and signal | LED-control timing, ADC sampling point, analog waveform and still-air stability | Timing/waveform captures where equipment is available, plus repeated raw logs |
| LCD integration | Correct values and recoverable communication behavior | LCD/UART comparison and documented failure cases |
| Buzzer integration | Commanded on/off operation without disrupting acquisition | Repeatable output tests using the final pin allocation |
| Alarm behavior | Explicit thresholds, hysteresis, invalid-data handling and reset behavior | Controlled test cases and observed outputs, clearly separated from safety certification |

This checklist describes an engineering approach, not a claim that every listed test has a published result. Its purpose is to establish where a failure occurs before adding more components or smoothing the displayed value. Filtering may improve presentation, but it should not conceal a wiring fault, incorrect timing or invalid data. Validation should use appropriate controlled inputs; deliberately creating smoke or dispersing dust is not a substitute for a sound bench test.

## My work and learning

I worked through component wiring, breadboard assembly, firmware build/upload attempts and bench troubleshooting, using serial output and multimeter measurements to investigate faults. Development was AI-assisted: code examples and debugging guidance were provided during the process. This project documents practical integration and debugging work rather than claiming independently authored, production-ready drivers.

Key lessons included matching the exact MCU target, keeping pin assignments consistent across standalone tests, distinguishing raw sensor values from calibrated measurements, and separating power, wiring, firmware and communication faults.

## Documentation to add

The project is complete. Further portfolio updates can attach the final firmware, final schematic/pin map, hardware photographs and reproducible build instructions, along with any available final test records. These are documentation additions, not a statement that the project is still in progress.

No calibrated concentration, independently reproduced performance benchmark, fabricated PCB or safety certification is claimed by this page.
