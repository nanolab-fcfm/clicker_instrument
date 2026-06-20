# Clicker

The Clicker Instrument is an automated button-pressing device designed to control the MHP30 hotplate's temperature settings. It uses an ESP8266 microcontroller to simulate precise button presses on the hotplate's physical interface, allowing remote temperature control via serial commands.

The device is integrated with laser_setup, which sends commands to set and reach target temperatures without manual button manipulation. It implements an intelligent algorithm that minimizes adjustment time by using long presses (500 ms) for large temperature increments and short presses (100 ms) for fine-tuning to the exact target.

## Key Features

- **Remote Temperature Control**: Set current and target temperatures via serial commands (SCT, STT)
- **Automatic Temperature Ramping**: Intelligent algorithm (`GO` command) that reaches the target temperature in minimal steps
- **Manual Button Control**: Direct button presses for manual operation (A, B, AA, BB, AB commands)
- **Query Capabilities**: Real-time temperature status queries (RCT, RTT)
- **Simple Serial Protocol**: Command-line interface at 115200 baud over USB-to-Serial

## Repository Structure

- **fw/clicker.ino**: Arduino firmware with all control logic
- **hw/**: KiCad PCB design (schematic, board layout, footprints)
- **hw/fab/**: Fabrication files (Gerber, drill files) for PCB manufacture
- **hw/nanolab_hw_library.kicad_sym**: Custom symbols for ESP8266 and temperature module
- **hw/nanolab_hw_library.pretty/**: Footprint library (ESP, Temperature_module)
