<div align="center">

# JZ Pico Tracker

**A custom schematic and PCB that integrates a handheld music tracker with plug-in commercial audio, storage and display modules.**

<p><img src="https://img.shields.io/badge/Complete%20-%20board%20fabricated,%20populated%20and%20tested-1a7f37" alt="Complete - board fabricated, populated and tested" height="20"></p>

<p><img src="https://img.shields.io/badge/KiCad-8250df" alt="KiCad" height="20"> <img src="https://img.shields.io/badge/Raspberry%20Pi%20Pico-8250df" alt="Raspberry Pi Pico" height="20"> <img src="https://img.shields.io/badge/PCB%20Design-8250df" alt="PCB Design" height="20"> <img src="https://img.shields.io/badge/MIDI-8250df" alt="MIDI" height="20"> <img src="https://img.shields.io/badge/Audio-8250df" alt="Audio" height="20"></p>

<img src="photos/08-assembled-tracker.jpg" width="880" alt="The assembled tracker showing the display, the keycaps and the two speaker grilles">

</div>

---

## At a glance

|  |  |
|---|---|
| **Focus** | Embedded audio and portable instrument |
| **Started** | June 2023 |
| **Status** | Complete - board fabricated, populated and tested |
| **Core hardware** | Raspberry Pi Pico, PCM5102 DAC, MAX98306 amplifier, SD card, ILI9341 display, MIDI out, Li-ion charging |
| **Design tools** | KiCad, reflow and hand soldering, microscope-assisted inspection, 3D printing |
| **Scope** | Full schematic capture, PCB layout, BOM, fabrication, assembly and bring-up |

## Overview

The picoTracker is a handheld music sampler built on the PicoTracker project, whose firmware is LittleGPTracker - also known as piggytracker. The reference hardware realises it as two boards sandwiched together. This project replaces that sandwich with a single custom PCB that carries the microcontroller, the full key field, the MIDI interface, the charging circuit and the power tree, and takes plug-in commercial modules for the parts that are already well characterised - the DAC, the amplifier, the storage and the display.

The board is built around a Raspberry Pi Pico, with a PCM5102 DAC breakout for I2S audio output and a MAX98306 class-D amplifier breakout driving two micro speakers. Storage is an Adafruit MicroSD breakout on SPI, and an ILI9341 SPI display plugs into a 14-pin header on the board. MIDI out is opto-isolated with an H11L1 optocoupler. A BQ24232 handles Li-ion charging from the USB-C port, and nine Kailh Choc switches are soldered straight to the board rather than fitted into sockets.

The power tree follows the arrangement documented in the Raspberry Pi Pico datasheet: the USB-C input feeds a power-path charger, and a P-channel MOSFET gates the battery path so that the cell is disconnected from the system rail whenever USB power is present.

**This board was designed against an older generation of the PicoTracker project and its hardware.** The upstream project has moved on since then - the fabrication warning that used to sit on the reference design has been resolved and the hardware has been revised - so this design should be read against the PicoTracker of its day rather than the current one.

### Goals

- Replace the two-board sandwich with a single carrier board that can be ordered from a fab and populated from a BOM.
- Keep the MIDI interface, the charging circuit and the power tree on the carrier board, and keep the switching amplifier physically away from the digital side so its noise stays out of the audio path.
- Keep the key field low-profile with Kailh Choc switches, matching the reference ergonomics.
- Use commercial modules where they are already well characterised - the DAC, the amplifier, the storage and the display - so the design effort goes into the parts that are specific to this board.

### Planning and design

The schematic was split into the functional blocks the board actually needs: MIDI out, the Raspberry Pi Pico, the SD card on SPI, the charge circuit, the power tree, the switch field, and the amplifier and speaker outputs. Working block by block made it possible to check each rail and each interface against the reference behaviour before the layout was committed.

The layout was driven by the display and the key field, since those fix the outline of the board. The Pico sits centrally with the modules radiating out from it, and the audio section is kept physically separate from the digital side to keep the amplifier switching noise off the DAC and the SD card.

Nine switch positions were laid out for Kailh Choc switches soldered directly to the board rather than dropped into sockets. The reference design makes the same call for the same reason: the depth behind the board is not enough to take pin sockets, and a thinner board is worth more than a tool-free switch change.

The power tree was laid out against the Raspberry Pi Pico datasheet's own guidance for running from a cell. A BQ24232 power-path charger takes the USB-C input, and a P-channel MOSFET sits between the battery and the system rail with its gate driven by VBUS, so plugging in USB disconnects the cell instead of running both sources at once.

#### Design decisions

| Decision | Reasoning |
|---|---|
| One carrier board instead of the two-board sandwich | One board means one fab order, one BOM and one assembly job. It does not mean every part lives on it - the DAC, the amplifier, the storage and the display are commercial modules that plug into it. |
| Solder the switches directly rather than fitting sockets | The space behind the board is too shallow for pin sockets, so the thinner board was worth more than being able to swap a switch without a soldering iron. |
| Keep the modules for SD, DAC and display rather than designing equivalents | They are all well-characterised breakouts. Replacing them would have added risk without adding understanding. |
| Opto-isolated MIDI out with an H11L1 | MIDI is a long, unbalanced bus that will pick up noise and ground offsets if it is wired directly to the logic ground. The H11L1 has a built-in Schmitt trigger and works at 3.3 V, so it pairs cleanly with a Pico. |
| BQ24232 charge controller rather than a simple TP4056 | It is a power-path charger, so it manages swapping between the cell and the input source while charging. Its status output is available, but it is not wired back to the microcontroller on this board. |
| Power path taken from the Raspberry Pi Pico datasheet | The datasheet documents how to run a Pico from a cell, and names the DMG2305UX P-channel MOSFET as the part to use for it. Following a published arrangement beat inventing one. |

### Key components

| Component | Specification | Qty |
|---|---|---|
| Raspberry Pi Pico | RP2040 microcontroller | 1 |
| PCM5102 DAC breakout | I2S audio output | 1 |
| MAX98306 breakout | Class-D amplifier for the speakers | 1 |
| Adafruit MicroSD breakout | SPI storage | 1 |
| ILI9341 SPI display module | Plugs into the 14-pin header on the board | 1 |
| BQ24232HRGTR | Power-path Li-ion charge controller, VQFN-16 | 1 |
| DMG2305UX | P-channel MOSFET for the power path, SOT-23 | 1 |
| H11L1 | Optocoupler for the isolated MIDI output, SMDIP-6 | 1 |
| PJ-327C-4A | 3.5 mm stereo headphone jacks | 2 |
| GT-USB-7010ASV | USB-C receptacle | 1 |
| Kailh Choc V1 | Low-profile key switches, soldered directly | 9 |
| MS-22D28-G020 | SPDT slide switches | 2 |
| 1N4148WS | Signal diode, SOD-323 | 1 |
| Ferrite beads | 600 R at 100 MHz, EMI suppression | 4 |
| Passives | 0603 resistors and capacitors | as listed |

### Build and testing

The board went from layout files to a fabricated PCB, through microscope inspection and rework, to a populated unit with the display and key field fitted.

<div align="center">

<table>
<tr>
<td width="33%" align="center"><img src="photos/01-pcb-layout.png" width="280" alt="KiCad PCB layout of the tracker board showing the key field, the display header and the audio section"><br><sub>PCB layout</sub></td>
<td width="33%" align="center"><img src="photos/02-schematic.png" width="280" alt="KiCad schematic divided into MIDI out, Raspberry Pi Pico, SD card on SPI, charge circuit, power, switches and amplifier blocks"><br><sub>Schematic</sub></td>
<td width="33%" align="center"><img src="photos/03-board-under-magnifier.jpg" width="280" alt="The board positioned under a stereo microscope on the bench"><br><sub>Under the microscope</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/04-microscope-inspection.jpg" width="280" alt="The board under the microscope with tweezers positioned over the fine-pitch parts"><br><sub>Microscope inspection</sub></td>
<td width="33%" align="center"><img src="photos/05-key-field.jpg" width="280" alt="The nine Kailh Choc switches soldered directly to the board, with the two speaker grilles either side"><br><sub>Key field</sub></td>
<td width="33%" align="center"><img src="photos/06-component-side.jpg" width="280" alt="The component side of the board held up"><br><sub>Component side</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/07-display-and-keys.jpg" width="280" alt="The board with the ILI9341 display module mounted and the switches in place"><br><sub>Display and keys</sub></td>
<td width="33%" align="center"><img src="photos/09-board-fully-populated.jpg" width="280" alt="The fully populated board held up, showing the Pico, the SD card breakout, the MIDI interface and the two speakers"><br><sub>Fully populated board</sub></td>
<td width="33%" align="center"><img src="photos/10-underside-silkscreen.jpg" width="280" alt="Underside of the board showing the silkscreen labels for the speakers, the power section and the Pico footprint"><br><sub>Underside silkscreen</sub></td>
</tr>
</table>
</div>

### Results

**What worked**

- Splitting the schematic into functional blocks made it straightforward to check the power tree and each interface independently.
- Keeping the amplifier section physically apart from the DAC and SD card kept the switching noise out of the audio path.
- Following the Raspberry Pi Pico datasheet for the power path meant the battery and the USB input could not back-feed each other, and the arrangement was already worked out and published rather than invented here.
- The low-profile Choc key field kept the tracker close to the same footprint as the reference design while the whole instrument still came together as one unit.

**What did not work**

- The MIDI interface needed extra attention, including the opto-isolator's output stage, and it took a second pass to get right.
- Inspecting the fine-pitch parts by eye was not good enough; the microscope turned out to be essential for verifying joints on the dense side of the board.
- The board needed rework after fabrication, so the first spin was not a first-pass success.
- The switches themselves proved less reliable than they should have been. That is a design problem rather than a layout one, so re-spacing the field would not fix it.
- The charger's status output is not wired back to the microcontroller, so the firmware currently has no way of reporting the charge state.

**What I would change in the next iteration**

- Swap the switch design - the switches chosen for this build proved less reliable than they should have been.
- Add charging status LEDs so the state of the cell is visible on the board itself.
- Wire the charger's status output back to the microcontroller so the firmware can report the charge state.
- Add the 3D-printed rear panel so the speaker cavities actually get the box resonance they need.
- Design a proper enclosure around the display and the key field rather than leaving the PCB exposed.
- Re-check the MIDI interface against the current PicoTracker hardware, since this board was designed against an older generation of it.

### Full build gallery

<div align="center">

<table>
<tr>
<td width="33%" align="center"><img src="photos/01-pcb-layout.png" width="280" alt="KiCad PCB layout of the tracker board showing the key field, the display header and the audio section"><br><sub>PCB layout</sub></td>
<td width="33%" align="center"><img src="photos/02-schematic.png" width="280" alt="KiCad schematic divided into MIDI out, Raspberry Pi Pico, SD card on SPI, charge circuit, power, switches and amplifier blocks"><br><sub>Schematic</sub></td>
<td width="33%" align="center"><img src="photos/03-board-under-magnifier.jpg" width="280" alt="The board positioned under a stereo microscope on the bench"><br><sub>Under the microscope</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/04-microscope-inspection.jpg" width="280" alt="The board under the microscope with tweezers positioned over the fine-pitch parts"><br><sub>Microscope inspection</sub></td>
<td width="33%" align="center"><img src="photos/05-key-field.jpg" width="280" alt="The nine Kailh Choc switches soldered directly to the board, with the two speaker grilles either side"><br><sub>Key field</sub></td>
<td width="33%" align="center"><img src="photos/06-component-side.jpg" width="280" alt="The component side of the board held up"><br><sub>Component side</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/07-display-and-keys.jpg" width="280" alt="The board with the ILI9341 display module mounted and the switches in place"><br><sub>Display and keys</sub></td>
<td width="33%" align="center"><img src="photos/09-board-fully-populated.jpg" width="280" alt="The fully populated board held up, showing the Pico, the SD card breakout, the MIDI interface and the two speakers"><br><sub>Fully populated board</sub></td>
<td width="33%" align="center"><img src="photos/10-underside-silkscreen.jpg" width="280" alt="Underside of the board showing the silkscreen labels for the speakers, the power section and the Pico footprint"><br><sub>Underside silkscreen</sub></td>
</tr>
<tr>
<td width="33%" align="center"><img src="photos/11-underside-full-board.jpg" width="280" alt="Full underside view of the finished board"><br><sub>Underside, full board</sub></td>
<td width="33%" align="center"><img src="photos/12-final-unit.jpg" width="280" alt="The finished tracker with the display lit and the keycaps fitted"><br><sub>The finished unit</sub></td>
</tr>
</table>
</div>

### Credits and references

- **picoTracker Alt-PCB** by [ijnekenamay](https://github.com/ijnekenamay/picotracker_alt-pcb) - the open-source reference design this project takes its architecture and ergonomics from (MIT). The schematic and PCB in this repository are my own.
- **LittleGPTracker** by the [PicoTracker project](https://github.com/xiphonics/picoTracker) - the firmware this hardware runs. This board was designed against an older generation of that project and its hardware.
- **[Raspberry Pi Pico datasheet](https://pip-assets.raspberrypi.com/categories/610-raspberry-pi-pico/documents/RP-008307-DS-2-pico-datasheet.pdf)** - sections 4.5 'Powering Pico' and 4.6 'Using a battery charger' are the reference this board's power path follows, down to the DMG2305UX P-channel MOSFET the datasheet recommends for exactly that job.

---

<p align="center"><sub>Part of the <a href="https://github.com/Panzer00Z">Panzer00Z</a> hardware portfolio &middot; <a href="https://github.com/Panzer00Z/Panzer00Z">profile &rarr;</a></sub></p>
