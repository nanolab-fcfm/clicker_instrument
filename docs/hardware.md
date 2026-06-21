# Hardware

## Interactive 3D model

Drag to rotate, scroll to zoom, right-drag to pan. This model is exported
directly from the KiCad board (`hw/clicker.kicad_pcb`) with `kicad-cli`.

<model-viewer
  src="../assets/3d/clicker.glb"
  alt="Interactive 3D model of the Clicker PCB with components"
  camera-controls
  auto-rotate
  touch-action="pan-y"
  shadow-intensity="1"
  exposure="1.1"
  style="width: 100%; height: 480px; background-color: #f5f5f5; border-radius: 8px;">
</model-viewer>

!!! note
    The board, copper, drill holes and the through-hole components (resistors
    `R101`–`R104`, transistors `Q101`/`Q102`, header `J101`) are rendered from
    their KiCad 3D models. The ESP8266 module and the temperature module use
    custom footprints that don't yet have 3D models assigned, so they appear as
    their footprint pads without a body. To regenerate the model after a board
    change (no system install needed):

    ```bash
    docker run --rm -e KICAD9_3DMODEL_DIR=/usr/share/kicad/3dmodels \
      -v "$PWD":/work -w /work kicad/kicad:9.0 bash -lc \
      'apt-get update -qq && apt-get install -y --no-install-recommends kicad-packages3d >/dev/null && \
       kicad-cli pcb export glb -f --include-tracks --include-zones --subst-models \
         -o docs/assets/3d/clicker.glb hw/clicker.kicad_pcb'
    ```

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
