# Hardware

## PCB Design

The Clicker is built on a custom KiCad PCB that interfaces an ESP8266 microcontroller with the actuators that press the MHP30's two buttons (A and B). The board uses a front/back copper stack (see the Gerbers below).

### PCB Files

All KiCad project files are located in the `hw/` directory:
- **clicker.kicad_sch**: Schematic diagram defining all electrical connections
- **clicker.kicad_pcb**: PCB layout with component placement and routing
- **clicker.kicad_pro**: KiCad project configuration

### Fabrication

Ready-to-manufacture Gerber files are in `hw/fab/`:
- **clicker-F_Cu.gbr**: Front copper layer
- **clicker-B_Cu.gbr**: Back copper layer
- **clicker-Edge_Cuts.gbr**: PCB outline
- **clicker-PTH.drl** / **clicker-NPTH.drl**: Plated and non-plated drill files
- **clicker-job.gbrjob**: Gerber job file with manufacturing notes

### Components

The custom symbol/footprint library (`hw/nanolab_hw_library.kicad_sym` and
`hw/nanolab_hw_library.pretty/`) defines the parts used on the board:

- **ESP** — the ESP8266 microcontroller module that runs the firmware and drives
  the two button outputs on **D1 (GPIO5)** and **D2 (GPIO4)**.
- **Temperature_module** — footprint for the temperature module.

!!! note
    For the authoritative component list and connections, open
    `hw/clicker.kicad_sch` in [KiCad](https://www.kicad.org/). The firmware pin
    assignment is documented on the [Firmware](firmware.md) page.

### Ordering the board

Upload the contents of `hw/fab/` to a PCB fab service (JLCPCB, OSH Park, …),
including the drill files (`*.drl`) for accurate hole drilling.
