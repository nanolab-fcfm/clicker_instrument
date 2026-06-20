# Usage

## Operating the Clicker Instrument

Once the device is flashed with fw/clicker.ino and connected via USB serial, you can control it from any serial terminal or Python script.

### Serial Connection

**Linux/macOS**:
```bash
minicom -D /dev/ttyUSB0 -b 115200
# or
screen /dev/ttyUSB0 115200
# or
python3 -m serial.tools.miniterm /dev/ttyUSB0 115200
```

**Windows**:
- Use PuTTY or Arduino IDE Serial Monitor (set baud to 115200)

If you get permission denied on Linux, add your user to the dialout group:
```bash
sudo usermod -aG dialout $USER
# Restart terminal after this
```

### Basic Temperature Control

**Manual sequence** (e.g., set and reach 80 °C):

```
SCT25       # Current temperature is 25°C
STT80       # Target is 80°C
RCT         # Response: 25
RTT         # Response: 80
GO          # Device automatically presses buttons to reach 80°C
```

The firmware will adjust the MHP30's current temperature from 25°C to 80°C using the intelligent ramping algorithm.

### Manual Button Control

Press individual buttons without automated ramping:

```
A           # Quick press button A once (100 ms)
AA          # Long press button A (500 ms) — increases temp by ~10°C
B           # Quick press button B once (100 ms)
BB          # Long press button B (500 ms) — decreases temp by ~10°C
AB          # Press both simultaneously (reset/confirm on some devices)
```

### Integration with laser_setup

The `laser_setup` platform integrates the Clicker Instrument via its serial interface:

1. **Connect** the clicker to the computer's USB port (e.g., `/dev/ttyUSB0`)
2. **Configure** laser_setup to use the clicker's port and baud rate (115200)
3. **Call commands** like `SCT`, `STT`, `GO` to automate hotplate temperature ramps during laser experiments

Example flow:
- User specifies experiment temperature profile (e.g., 30°C → 100°C → 30°C)
- laser_setup sends `SCT30`, `STT100`, `GO` to the clicker
- Clicker automatically adjusts the MHP30 without user intervention
- laser_setup polls `RCT` to verify current temperature between steps

### Troubleshooting

**Device not responding**:
- Check USB connection and baud rate (must be 115200)
- Verify correct serial port is selected
- Press Ctrl+C in minicom/screen to reset the connection

**Button presses not working**:
- Confirm GPIO pins D1 (GPIO5) and D2 (GPIO4) are wired correctly
- Check relay/solenoid drivers are powered
- Test with `A` command; verify mechanical solenoid activates

**Temperature not reaching target**:
- Verify CT and TT are set correctly with `RCT`/`RTT`
- Check MHP30 is in a mode that responds to button presses
- Increase `pressDelay` variable in firmware if presses are being missed

**Device identification**:
Send `*IDN?` — should respond:
```
ROSATECH,CLK100,P0000003,1.0.0
```
If not present in response, re-flash the firmware.
