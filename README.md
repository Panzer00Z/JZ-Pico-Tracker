<div align="center">

# JZ Pico Tracker

**A custom schematic and PCB that puts an entire handheld music tracker onto one board.**

<p><img src="https://img.shields.io/badge/Complete%20-%20board%20fabricated,%20populated%20and%20tested-1a7f37" alt="Complete - board fabricated, populated and tested" height="20"> &nbsp; <img src="https://img.shields.io/badge/Big%20Project-8250df" alt="Big Project" height="20"></p>

<p><img src="https://img.shields.io/badge/KiCad-8250df" alt="KiCad" height="20"> <img src="https://img.shields.io/badge/Raspberry%20Pi%20Pico-8250df" alt="Raspberry Pi Pico" height="20"> <img src="https://img.shields.io/badge/PCB%20Design-8250df" alt="PCB Design" height="20"> <img src="https://img.shields.io/badge/MIDI-8250df" alt="MIDI" height="20"> <img src="https://img.shields.io/badge/Audio-8250df" alt="Audio" height="20"></p>

<img src="photos/12-assembled-tracker.jpg" width="880" alt="The assembled tracker showing the display, the seven visible keycaps and the two speaker grilles">

</div>

---

## At a glance

|  |  |
|---|---|
| **Category** | Big Project &middot; Embedded audio and portable instrument |
| **Started** | June 2023 |
| **Status** | Complete - board fabricated, populated and tested |
| **Core hardware** | Raspberry Pi Pico, PCM5102 DAC, MAX98306 amplifier, SD card, MIDI out, Li-ion charging |
| **Design tools** | KiCad, reflow and hand soldering, microscope-assisted inspection, 3D printing |
| **Scope** | Full schematic capture, PCB layout, BOM, fabrication, assembly and bring-up |

## Overview

The picoTracker is a handheld music sampler based on the LittleGPTracker project, and the reference hardware realises it as two boards sandwiched together. This project consolidates that into a **single custom PCB** carrying the microcontroller, audio chain, storage, MIDI output, charging and the full key field.

The board is built around a Raspberry Pi Pico with a PCM5102 DAC for output and a MAX98306 class-D amplifier driving two micro speakers. Storage is an Adafruit MicroSD breakout on SPI, and MIDI out is opto-isolated with a 6N138. A BQ24232 handles Li-ion charging from the USB-C port, and nine Kailh Choc switches sit in hot-swap sockets so a failed switch can be changed without a soldering iron.

The reference project warns against fabricating because of a mistake around the MIDI interface. That is exactly the area this board concentrates on, and the board was built and tested around the MIDI path specifically.

### Goals

- Replace the two-board sandwich with one board that can be ordered from a fab and populated from a BOM.
- Keep the audio chain, storage, MIDI and charging on the same board without introducing ground or power noise between them.
- Use hot-swap sockets so a switch can be replaced in seconds.
- Keep the key field low-profile with Kailh Choc switches, matching the reference ergonomics.
- Have the BOM be a real, orderable part list rather than an assembly of loose breakouts.

### Planning and design

The schematic was split into the functional blocks the board actually needs: MIDI out, the Raspberry Pi Pico, the SD card on SPI, the charge circuit, the power tree, the switch field, and the amplifier and speaker outputs. Working block by block made it possible to check each rail and each interface against the reference behaviour before the layout was committed.

The layout was driven by the display and the key field, since those fix the outline of the board. The Pico sits centrally with the modules radiating out from it, and the audio section is kept physically separate from the digital side to keep the amplifier switching noise off the DAC and the SD card.

Nine switch positions were laid out as hot-swap sockets so that a worn switch can be pulled and replaced. That choice constrains the footprint spacing, which is why the switch field took two attempts.

#### Design decisions

| Decision | Reasoning |
|---|---|
| Single board instead of the two-board sandwich | One board means one fab order, one BOM and one assembly job, and it removes the inter-board connector that was the reference design's most failure-prone joint. |
| Hot-swap sockets for all nine switches | A switch is the most likely part to fail on a portable device. Sockets make it a ten-second swap instead of a desoldering job. |
| Keep the modules for SD and DAC rather than designing equivalents | Both are well-characterised breakouts. Replacing them would have added risk without adding understanding. |
| Opto-isolated MIDI out with a 6N138 | MIDI is a long, unbalanced bus that will pick up noise and ground offsets if it is wired directly to the logic ground. |
| BQ24232 charge controller rather than a simple TP4056 | It handles the charging state machine properly and reports status back to the microcontroller. |

### Key components

| Component | Specification | Qty |
|---|---|---|
| Raspberry Pi Pico | RP2040 microcontroller | 1 |
| PCM5102 DAC breakout | I2S audio output | 1 |
| MAX98306 breakout | Class-D amplifier for the speakers | 1 |
| Adafruit MicroSD breakout | SPI storage | 1 |
| BQ24232HRGTR | Li-ion charge controller, VQFN-16 | 1 |
| DMG2305UX | N-channel MOSFET, SOT-23 | 1 |
| H11L1 | Low-dropout regulator | 1 |
| PJ-327C-4A | 3.5 mm stereo headphone jacks | 2 |
| GT-USB-7010ASV | USB-C receptacle | 1 |
| Kailh Choc V1 | Low-profile key switches | 9 |
| MS-22D28-G020 | SPDT slide switches | 2 |
| 1N4148WS | Signal diode, SOD-323 | 1 |
| Ferrite beads | 600 R at 100 MHz, EMI suppression | 4 |
| Passives | 0603 resistors and capacitors | as listed |

### Build and testing

The board went from layout files to a fabricated PCB, through microscope inspection and rework, to a populated unit with the display and key field fitted.

<div align="center">

<table>
<tr>
<td width="33%" align="center"><img src="photos/01-pcb-layout.png" width="280" alt="KiCad PCB layout of the tracker board showing the key field, display header and the audio section"><br><sub>PCB layout</sub></td>
<td width="33%" align="center"><img src="photos/02-schematic.png" width="280" alt="KiCad schematic divided into MIDI out, Raspberry Pi Pico, SD card on SPI, charge circuit, power, switches and amplifier blocks"><br><sub>Schematic</sub></td>
<td width="33%" align="center"><img src="photos/03-board-under-magnifier.jpg" width="280" alt="The board positioned under a stereo microscope on the bench"><br><sub>Under the microscope</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/04-microscope-inspection.jpg" width="280" alt="The board under the microscope with tweezers positioned over the fine-pitch parts"><br><sub>Microscope inspection</sub></td>
<td width="33%" align="center"><img src="photos/05-board-edge-headers.jpg" width="280" alt="Edge-on view of the board showing the three-pin headers along the sides"><br><sub>Board edge and headers</sub></td>
<td width="33%" align="center"><img src="photos/06-key-field-hot-swap-sockets.jpg" width="280" alt="Underside of the board showing the nine Kailh Choc hot-swap sockets and the two speaker grilles"><br><sub>Key field, hot-swap sockets</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/07-component-side.jpg" width="280" alt="The component side of the board held up"><br><sub>Component side</sub></td>
<td width="33%" align="center"><img src="photos/08-hot-swap-socket-detail.jpg" width="280" alt="Close-up of a single hot-swap socket with a Choc switch seated in it"><br><sub>Hot-swap socket detail</sub></td>
<td width="33%" align="center"><img src="photos/09-board-with-display-fitted.jpg" width="280" alt="The board with the display fitted at the top and the key field populated"><br><sub>Display and key field fitted</sub></td>
</tr>
</table>
</div>

### Results

**What worked**

- Splitting the schematic into functional blocks made it straightforward to check the power tree and each interface independently.
- The single-board approach removed the inter-board connector entirely, which was the part of the reference design most likely to cause trouble.
- Hot-swap sockets worked exactly as intended - a switch was changed in seconds during testing.
- Keeping the amplifier section physically apart from the DAC and SD card kept the switching noise out of the audio path.
- The BQ24232 gave proper charge-state reporting back to the Pico, so the firmware can show battery status rather than guessing.

**What did not work**

- The first switch layout did not leave enough clearance between the hot-swap sockets, so the field was re-spaced.
- The reference project's warning about the MIDI section was warranted - the MIDI interface needed extra attention, including attention to the opto-isolator's output stage.
- Inspecting the fine-pitch parts by eye was not good enough; the microscope turned out to be essential for verifying joints on the dense side of the board.
- The board needed rework after fabrication, so the first spin was not a first-pass success.
- The reference project ships a warning against fabricating. That warning should be taken seriously by anyone who picks this design up.

**What I would change in the next iteration**

- Revise the layout with the switch-field spacing corrected and the rework points removed, so the next spin is a first-pass success.
- Add the 3D-printed rear panel so the speaker cavities actually get the box resonance they need.
- Design a proper enclosure around the display and the key field rather than leaving the PCB exposed.
- Add a second DAC or an audio input so the tracker can be used as an input device as well as a sampler.
- Reduce the board outline so it matches the reference footprint more closely.

### Full build gallery

<div align="center">

<table>
<tr>
<td width="33%" align="center"><img src="photos/01-pcb-layout.png" width="280" alt="KiCad PCB layout of the tracker board showing the key field, display header and the audio section"><br><sub>PCB layout</sub></td>
<td width="33%" align="center"><img src="photos/02-schematic.png" width="280" alt="KiCad schematic divided into MIDI out, Raspberry Pi Pico, SD card on SPI, charge circuit, power, switches and amplifier blocks"><br><sub>Schematic</sub></td>
<td width="33%" align="center"><img src="photos/03-board-under-magnifier.jpg" width="280" alt="The board positioned under a stereo microscope on the bench"><br><sub>Under the microscope</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/04-microscope-inspection.jpg" width="280" alt="The board under the microscope with tweezers positioned over the fine-pitch parts"><br><sub>Microscope inspection</sub></td>
<td width="33%" align="center"><img src="photos/05-board-edge-headers.jpg" width="280" alt="Edge-on view of the board showing the three-pin headers along the sides"><br><sub>Board edge and headers</sub></td>
<td width="33%" align="center"><img src="photos/06-key-field-hot-swap-sockets.jpg" width="280" alt="Underside of the board showing the nine Kailh Choc hot-swap sockets and the two speaker grilles"><br><sub>Key field, hot-swap sockets</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/07-component-side.jpg" width="280" alt="The component side of the board held up"><br><sub>Component side</sub></td>
<td width="33%" align="center"><img src="photos/08-hot-swap-socket-detail.jpg" width="280" alt="Close-up of a single hot-swap socket with a Choc switch seated in it"><br><sub>Hot-swap socket detail</sub></td>
<td width="33%" align="center"><img src="photos/09-board-with-display-fitted.jpg" width="280" alt="The board with the display fitted at the top and the key field populated"><br><sub>Display and key field fitted</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/10-modules-and-speakers.jpg" width="280" alt="The board with the Pico, the SD card breakout, the MIDI interface and both micro speakers mounted"><br><sub>Modules and speakers</sub></td>
<td width="33%" align="center"><img src="photos/11-display-and-keys.jpg" width="280" alt="The board with the ILI9341 display module mounted and the switches in place"><br><sub>Display and keys</sub></td>
<td width="33%" align="center"><img src="photos/13-status-leds-lit.jpg" width="280" alt="Component side with the status LEDs lit in red, green and orange"><br><sub>Status LEDs lit</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/14-led-through-board.jpg" width="280" alt="Back of the board with the RGB status LEDs glowing through their holes"><br><sub>Status LEDs through the board</sub></td>
<td width="33%" align="center"><img src="photos/15-board-fully-populated.jpg" width="280" alt="The fully populated board held up, showing the Pico, the SD card breakout, the MIDI interface and the two speakers"><br><sub>Fully populated board</sub></td>
<td width="33%" align="center"><img src="photos/16-underside-silkscreen.jpg" width="280" alt="Underside of the board showing the silkscreen labels for the speakers, the power section and the Pico footprint"><br><sub>Underside silkscreen</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/17-underside-full-board.jpg" width="280" alt="Full underside view of the finished board"><br><sub>Underside, full board</sub></td>
<td width="33%" align="center"><img src="photos/18-final-unit.jpg" width="280" alt="The finished tracker with the display lit and the keycaps fitted"><br><sub>The finished unit</sub></td>
</tr>
</table>
</div>

### Credits and references

- **picoTracker Alt-PCB** by [ijnekenamay](https://github.com/ijnekenamay/picotracker_alt-pcb) - the open-source reference design this project takes its architecture and ergonomics from (MIT). The schematic and PCB in this repository are my own.
- **LittleGPTracker** by the LittleGPTracker project - the firmware this hardware runs.
- The PicoTracker project carries an explicit warning not to fabricate because of a known problem around the MIDI interface. That warning applies here too.

---

<p align="center"><sub>Part of the <a href="https://github.com/Panzer00Z">Panzer00Z</a> hardware portfolio &middot; <a href="https://github.com/Panzer00Z/Panzer00Z">profile &rarr;</a></sub></p>
