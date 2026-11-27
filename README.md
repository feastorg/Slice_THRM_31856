<div align="center">
  <!-- Title: -->
  <a href="https://github.com/uwo-fast">
    <img src="https://github.com/uwo-fast/.github/blob/main/branding/FAST%20Logo%20Orange%20on%20White%20Transparent.png" height="100">
  </a>
  <h2><a href="https://github.com/uwo-fast/Slice_THRM"> Slice THRM </a></h2>
  <!-- Labels: -->
  <!-- First row: -->
  <a href="https://github.com/uwo-fast/.github/blob/main/collaborating.md">
    <img src="https://img.shields.io/badge/Collaborators-Welcome-lightgreen" alt="Collaborators">
  </a>
  <a href="https://github.com/uwo-fast/.github/blob/main/contributing.md">
    <img src="https://img.shields.io/badge/Contributions-Welcome-lightgreen" alt="Contributions">
  </a>
  <a href="https://www.appropedia.org/Category:FAST_literature_reviews">
    <img src="https://img.shields.io/badge/Appropedia-Lit_Review-white" alt="Appropedia">
  </a>
  <a href="https://certification.oshwa.org/">
    <img src="https://img.shields.io/badge/OSHWA-Incomplete-cc3300?style=flat-square" height="20" alt="OSHWA">
    <!-- img src="https://img.shields.io/badge/OSHWA-Certified-darkgreen?style=flat-square" height="20" alt="OSHWA" -->       <!-- img src="https://img.shields.io/badge/OSHWA-Pending-darkorange?style=flat-square" height="20" alt="OSHWA" -->
  </a>
  <a href="https://osf.io/">
    <img src="https://img.shields.io/badge/OSF-Project-lightblue" alt="OSF">
  </a>
  <a href="https://doi.org">
    <img src="https://img.shields.io/badge/Paper-Unsubmitted-ff0066" alt="Paper">
  </a>
  <!-- Second row: -->
  <br>
  <a href="https://github.com/uwo-fast/Slice_THRM/blob/main/LICENSE">
    <img src="https://img.shields.io/github/license/uwo-fast/Slice_THRM" alt="License">
  </a>
  <a href="https://github.com/uwo-fast/Slice_THRM">
    <img src="https://img.shields.io/github/repo-size/uwo-fast/Slice_THRM" alt="Repo Size">
  </a>
  <a href="https://github.com/uwo-fast/Slice_THRM/issues">
    <img src="https://img.shields.io/github/issues/uwo-fast/Slice_THRM" alt="Issues">
  </a>
  <a href="https://github.com/uwo-fast/Slice_THRM/network/members">
    <img src="https://img.shields.io/github/forks/uwo-fast/Slice_THRM?style=social" alt="Forks">
  </a>
  <a href="https://github.com/uwo-fast/Slice_THRM/stargazers">
    <img src="https://img.shields.io/github/stars/uwo-fast/Slice_THRM?style=social" alt="Stars">
  </a>
  <!-- Short description: -->
  <h3>Thermocouple Reader Slice for BREAD.</h3>
</div>


## Introduction
A BREAD slice that reads four thermocouples. Each channel has its own
MAX31856 (cold-junction compensated, linearized, types B/E/J/K/N/R/S/T),
read by the slice's Arduino Nano over one shared SPI bus.

## Project Overview

### Channels

| Channel | Terminal | Converter | Nano CS | Nano DRDY | FAULT (through U2) |
| ------- | -------- | --------- | ------- | --------- | ------------------ |
| 1 | J4 (pin 1 T+, pin 2 T-) | U4 | D6 | A3 | A7 with D10 low |
| 2 | J5 | U5 | D7 | A2 | A7 with D10 high |
| 3 | J6 | U6 | D8 | A1 | A6 with D10 low |
| 4 | J7 | U7 | D9 | A0 | A6 with D10 high |

FAULT and DRDY are active low. The four FAULT lines reach the Nano's
analog-only pins A6/A7 through U2 (CD74HC4053): D10 drives S1 and S2
together, so one D10 level selects channels 1 and 3, the other 2 and 4.
FAULT1-4 have 10k pull-ups to 3.3 V, so an idle line reads about 3.3 V
on the 0-5 V ADC and an asserted fault reads near 0 V.

### SPI

- Shared SCK (D13), SDI/MOSI (D11), SDO/MISO (D12); one CS per channel.
- SPI mode 1 or 3: the MAX31856 needs CPHA = 1 and accepts either
  clock polarity (data sheet 19-7534 Rev 0, Table 5).
- SCK up to 4 MHz (16 MHz / 4). The MAX31856 allows 5 MHz; the level
  buffers add a few ns each way, so check SDO timing on the bench before
  going faster than 4 MHz.
- D10 is the AVR's hardware SS pin; it is an output here (FAULT_SW), which
  keeps the SPI peripheral in master mode.

### 3.3 V domain and level shifting

The MAX31856s run from +3V3 (U3, MIC5225-3.3, 150 mA). The Nano is 5 V.

- U8 (74LVC245A, on +3V3) passes CS1-4, SCK and SDI from the Nano to the
  converters at 3.3 V. Its inputs accept 5 V.
- U9 (74AHCT245, on +5V) passes SDO and DRDY1-4 back to the Nano. Its TTL
  inputs (VIH 2.0 V) read the 3.3 V outputs with margin.
- R21-R24 hold CS1-4 high while the Nano is in reset, so no converter is
  selected. R25 holds SDO low when no converter drives it.
- U9 drives the Nano's MISO (D12) at all times: program the Nano over USB,
  not through an ICSP header plugged into the Nano.

### Qwiic (J1)

J1 is still powered from +5V on this revision. A Qwiic device expects
3.3 V on its supply and I/O, and the slice's I2C bus (A4/A5 and the bus
connector) is a 5 V bus, so J1 needs a level translator before it is safe
for 3.3 V-only Qwiic boards. Do not plug a 3.3 V-only Qwiic board into J1.

### Firmware

`firmware/` is empty; firmware is tracked separately.

## Installation
[Provide instructions for installing any necessary software or hardware components.]

## Usage
[Explain how to use the project. Include examples and screenshots if applicable.]

### Basic Usage
[Provide basic usage instructions.]

### Advanced Usage
[Provide advanced usage instructions, including any optional features or configurations.]

## Documentation
[Link to the detailed documentation if available. Provide instructions on how to access and navigate the documentation.]

## Contributing
[Explain how others can contribute to the project. Include guidelines for submitting issues, pull requests, and any coding standards.]

### Code of Conduct
[Link to the project's code of conduct if available.]

## License
[State the license under which the project is distributed. Include a link to the full license text.]

## Acknowledgements
[Acknowledge any contributors, collaborators, funding sources, or organizations that supported the project.]

## Contact
[Provide contact information for the project maintainer(s) or lead researcher(s). Include an email address or a link to a contact form.]

---

<div align="center">
  <p>
    <img src="https://img.shields.io/badge/Developed_by-UWO--FAST-orange" alt="Developed by UWO-FAST">
    <img src="https://img.shields.io/badge/Powered_by-Open_Source-blue" alt="Powered by Open Source">
  </p>
</div>
