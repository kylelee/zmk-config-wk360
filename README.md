# WK Kinesis Wireless - ZMK Firmware Config

Justified the wireless/bluetooth to default OFF in config, and support wire only mode between left and right keyboard.

Split wireless keyboard firmware configuration built on [ZMK Firmware](https://github.com/zmkfirmware/zmk), targeting the **WK 360** ergonomic split keyboard.


## Hardware

- **Board:** nice!nano v2 (nRF52840)
- **Keyboards:** WK Kinesis Wireless (split left / right)
- **Interface:** Bluetooth via built-in nRF52840 radio; USB-C for firmware updates and USB mode

## Keymap Features

| Feature | Detail |
| --- | --- |
| Layout | Kinesis-style split columns with center column block |
| Layers | 7 layers — Base (Base), Layer 1, Layer 2, Layer 3–6 |
| Home Row Mods | `hold-tap` (tap-preferred), tapping-term **200 ms**, quick-tap **175 ms** |
| Bluetooth | Multi-device pairing via Layer 2 (`BT_SEL 0-4`) |
| RGB Lighting | Toggle and effects control via Layer 2 |

### Layers Overview

| Layer | Purpose |
| --- | --- |
| Base | Primary typing layout (QWERTY) |
| 1 | Function row (F1–F12), cursor/arrow cluster on home column |
| 2 | Bluetooth pairing, RGB effects, clear-all shortcut |
| 3–6 | Empty slots ready for user customization |

## Building

This config uses GitHub Actions via `build.yaml`:

```bash
# Compile left half (studio + USB UART mode)
gh workflow run Build -f input=keymap -f keyboard=wk_kinesis_left_studio

# Compile right half
gh workflow run Build -f input=keymap -f keyboard=wk_kinesis_right

# Compile settings reset key combo
gh workflow run Build -f input=keymap -f keyboard=settings_reset
```

Build artifacts are uploaded to the Actions tab and can be downloaded as `.uf2` files.

## Flashing

1. Download the compiled `.uf2` file from GitHub Actions.
2. Hold the **BOOT** button on your nice!nano, then plug in USB-C.
3. Drag-and-drop the `.uf2` file into the mounted `RPI-RP2` drive.
4. The keyboard will reset automatically after flashing.

### Studio Mode (Left Half)

The left half is built with **ZMK Studio** enabled by default (`-DCONFIG_ZMK_STUDIO=y`). This allows:

- Real-time keymap editing and layer switching via USB HID mouse
- Visual firmware feedback on the host PC

> **Note:** Disable `studio-rpc-usb-uart` for production use to free up UART resources. The non-studio build (`wk_kinesis_left`) is also provided alongside.

## Project Structure

```
config/
├── boards/shields/wk_kinesis   # Board and shield definitions for ZMK
│   ├── wk_kinesis.dtsi          # Key layout (JSON pin map)
│   └── ...
├── west.yml                     # Dependencies: pulls upstream ZMK
├── wk_kinesis.keymap            # User keymap definition
└── wk_kinesis.json              # Keyboard physical layout
build.yaml                       # GitHub Actions build matrix
```

## Customization

- **Keymap:** Edit `config/wk_kinesis.keymap` for layer and behavior changes.
- **Layout:** Modify the JSON pin map in the shield directory to match your key arrangement.
- **Homerow mods timing:** Adjust `tapping-term-ms` / `quick_tap_ms` in `behaviors.dtsi` for different typing styles.

## References

- [ZMK Firmware](https://zmk.dev/) — Documentation and wiki
- [nice!nano](https://nicekeyboards.com/nice-nano) — Board details
- [WK Kinesis](https://github.com/Kaiya7/WK-Kinesis) — Keyboard hardware reference (if applicable)
