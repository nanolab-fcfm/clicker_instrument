# Firmware

## Arduino Firmware

The firmware is a single Arduino sketch: **fw/clicker.ino** (C++ / Arduino IDE).

### Microcontroller & Board

- **Target Board**: ESP8266 (NodeMCU or equivalent)
- **Baud Rate**: 115200
- **Clock**: 80 MHz (default)

### Building & Flashing

1. **Install Arduino IDE** and the ESP8266 board package:
   - Board: ESP8266 Generic Module
   - Flash Size: 4MB / 1MB (SPIFFS)
   
2. **Open fw/clicker.ino** in Arduino IDE

3. **Flash to device**:
   - Select Tools → Board → ESP8266
   - Select Tools → Port → (your USB port, typically /dev/ttyUSB0 or /dev/cu.usbserial-*)
   - Sketch → Upload

### GPIO Pinout

```
D1 (GPIO5)  → Button A output (short/long press control)
D2 (GPIO4)  → Button B output (short/long press control)
```

Both pins start LOW and are pulled HIGH during a button press.

### Serial Protocol (115200 baud)

All commands are received as strings terminated by newline (`\n`) or carriage return (`\r`).

#### Temperature Commands

| Command | Example | Description |
|---------|---------|-------------|
| `SCTxxx` | `SCT30` | Set current temperature to xxx °C (stored in variable CT) |
| `STTxxx` | `STT100` | Set target temperature to xxx °C (stored in variable TT) |
| `RCT` | `RCT` | Query current temperature (responds with integer) |
| `RTT` | `RTT` | Query target temperature (responds with integer) |
| `GO` | `GO` | Automatically adjust from CT to TT using intelligent ramping algorithm |
| `*IDN?` | `*IDN?` | Query device identification (responds: `ROSATECH,CLK100,P0000003,1.0.0`) |

#### Manual Button Press Commands

| Command | Duration | Description |
|---------|----------|-------------|
| `A` | 100 ms | Short press button A |
| `AA` | 500 ms | Long press button A |
| `B` | 100 ms | Short press button B |
| `BB` | 500 ms | Long press button B |
| `AB` | 100 ms | Simultaneous short press of both A and B |

Unknown commands produce: `ERROR! Unknown command: <cmd>`

### Temperature Ramping Algorithm

The `GO` command executes `reachTargetTemperature()`, which:

1. Sends 2× AB (both buttons) to reset the MHP30 display
2. Sends 2× short press A to initialize the system
3. Uses large steps (long press A/B) to approach the target in 10 °C increments
4. Switches to single-step (short press A/B) for final 1 °C adjustments
5. Prevents overshoot with boundary checks

The algorithm minimizes time by:
- Long presses increase/decrease by 10 °C
- Short presses increase/decrease by 1 °C
- Detects direction (CT < TT vs CT > TT) and uses button A (up) or B (down)

### Key Variables

- **CT**: Current temperature (integer, °C)
- **TT**: Target temperature (integer, °C)
- **pressDelay**: Delay between successive presses (default 500 ms, tunable)

### Requirements

- Arduino IDE (or PlatformIO)
- ESP8266 board support package
- No external libraries required (uses only Serial and digitalWrite)
