# FPGA-Based Digital Synthesizer and Metronome on Basys3

This project implements a digital audio synthesizer and metronome on a Xilinx Basys3 FPGA board (Artix-7 XC7A35T). The Basys3 is an entry-level development board featuring an Artix-7 FPGA, 100 MHz onboard clock, 16 user switches, 16 LEDs, 5 pushbuttons, and a 4-digit 7-segment display. 
A numeric keypad (4×4 matrix) is used for inputting musical notes, a speaker outputs synthesized audio, and a piezo buzzer serves as a metronome tick. The design uses a MicroBlaze soft-core processor to run control software and custom AXI peripherals to interface with the keypad, speaker, and buzzer. In this setup, the keys drive a sine-wave generator via PWM to produce audible notes, while the buzzer is toggled at regular intervals to simulate a metronome.

> Unfortunately, I don’t have a demo available anymore since I no longer own the Basys3 board. This project is much more about embedded systems engineering than musical technology.

Authors: David Cendejas Rodríguez & Marcos Gómez Cortés
---

## Hardware Platform

- **FPGA Board:** Basys3 Artix-7 (XC7A35T, 100 MHz clock). Provides plenty of I/O (switches, LEDs, etc.) for this application.  
- **Input Keypad:** 4×4 matrix of buttons for note selection, scanned by custom logic.  
- **Audio Output:** PWM-driven speaker output, converting digital sine values into analog-like signals.  
- **Metronome Buzzer:** Piezo buzzer connected via AXI GPIO, toggled to create tempo clicks.  
- **Displays:** Onboard 7-segment display and LEDs show tempo or active beats.

---

## System Architecture

The system uses a **MicroBlaze soft processor core** as controller. 
Key components:

- **Memory:** On-chip Block RAM with instruction/data caches.  
- **Peripherals (Custom AXI IP):**
  - Keypad Scanner: Drives keypad rows, reads columns, debounces inputs.  
  - Synthesizer Generator: Generates waveforms using a 64-sample sine ROM.  
  - PWM Output: Converts audio samples into PWM signals for the speaker.  
  - Buzzer (GPIO): Simple AXI GPIO toggling buzzer pin.  
  - Timers: Schedule tempo ticks and note durations.

---

## Audio Synthesis Design

- Generates tones via digital sine waves.  
- Uses **PWM** to emulate analog audio.  
- Sine-wave generator core stores 64 amplitude values in ROM.  
- Frequency calculation:  
  \[
  ClkDiv = \frac{100\,\text{MHz}}{f \cdot 64}
  \]  
- Supports polyphony (up to 4 notes simultaneously).  
- Standard FPGA audio technique with clock-based lookup and PWM.

---

## Key Input (Matrix Keypad)

- 4×4 keypad scanned by driving rows and reading columns.  
- Each key mapped to a musical note or command (e.g., octave shift).  
- Software activates sine-wave generators upon keypress.  
- Supports up to 16 keys, typically 12 for one octave.

---

## MicroBlaze Software

Tasks handled by C code:

- **Scanning Keypad:** Detect presses/releases via custom peripheral.  
- **Scheduling Audio Output:** Maintain active notes, sum outputs, write to PWM.  
- **Metronome Control:** Set tempo via switches, toggle buzzer GPIO, display BPM.  
- **AXI Bus:** Peripherals appear as memory-mapped registers.  
- Uses Xilinx BSP drivers and xparameters.h for initialization.

---

## Metronome (Buzzer) Design

- Tempo range: 60–200 BPM.  
- Delay count calculated from 100 MHz clock.  
- Buzzer pin toggled high/low at each beat.  
- Tempo input via switches, displayed on 7-seg display.  
- Supports beat tracking (e.g., 4/4 or 3/4) with LED indicators.

---

## Usage and Operation

1. **Hardware Setup:** Connect amplifier/speaker to PWM output, buzzer to GPIO, keypad to FPGA pins.  
2. **Bitstream & Software:** Program FPGA with Vivado bitstream, load MicroBlaze firmware.  
3. **Playing Notes:** Press keypad keys to play notes (up to 4 simultaneously).  
4. **Adjusting Tempo:** Use switches or keys to set BPM, displayed on 7-seg.  
5. **Performance:** Real-time audio and metronome with minimal latency.

---

## Conclusion

This project combines **embedded system design** and **FPGA digital logic**. It demonstrates:

- Custom IP integration (keypad interface, PWM audio).  
- Soft processor (MicroBlaze) for high-level control.  
- Real-time audio synthesis using PWM-driven sine waves.  
- Practical use of Basys3’s I/O and Vivado toolchain for multimedia applications
