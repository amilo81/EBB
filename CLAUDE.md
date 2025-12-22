# CLAUDE.md - AI Assistant Guide for EBB Repository

## Repository Overview

This repository contains hardware designs, firmware, and configuration files for **BigTreeTech EBB (Electronic Breakout Board)** CAN bus toolhead boards for 3D printers running Klipper firmware. These boards are designed for Voron and similar CoreXY 3D printers, providing a compact CAN bus solution for toolhead electronics.

**Official Project**: BigTreeTech EBB CAN Bus Boards
**Firmware Support**: Klipper only
**Primary Use Case**: Toolhead control boards with CAN bus communication

## Codebase Structure

### Directory Organization

```
EBB/
├── EBB CAN V1.0 (STM32F072)/          # First generation - STM32F072 MCU
│   ├── EBB36 CAN V1.0/                # 36mm version
│   │   ├── Hardware/                   # Schematics, BOM, pinout diagrams
│   │   └── 3D/                        # STL files for mounting
│   ├── EBB42 CAN V1.0/                # 42mm version
│   │   ├── Hardware/
│   │   └── 3D/
│   ├── firmware_USB.bin               # Precompiled USB firmware
│   ├── firmware_canbus.bin            # Precompiled CAN bus firmware
│   └── sample-bigtreetech-ebb-canbus-v1.0.cfg  # Klipper config

├── EBB CAN V1.1 (STM32G0B1)/          # Second generation - STM32G0B1 MCU
│   ├── EBB36 CAN V1.1/                # 36mm version
│   ├── EBB42 CAN V1.1/                # 42mm version
│   ├── firmware_USB.bin
│   ├── firmware_canbus.bin
│   ├── sample-bigtreetech-ebb-canbus-v1.1.cfg  # V1.1 config
│   └── sample-bigtreetech-ebb-canbus-v1.2.cfg  # V1.2 config (PA2→PB13)

├── EBB SB2240_2209 CAN/               # StealthBurner compatible boards
│   ├── SB2209/                        # TMC2209 stepper driver version
│   │   ├── Hardware/
│   │   ├── 3D/
│   │   └── README.md
│   ├── SB2240/                        # TMC2240 stepper driver version
│   │   ├── Hardware/
│   │   └── 3D/
│   ├── STL/                           # Modified StealthBurner parts
│   ├── CAD/                           # Source CAD files
│   ├── Build Guide/                   # Installation documentation
│   ├── firmware_*.bin                 # Various firmware builds
│   ├── readme.md                      # 3D printed parts info
│   └── sample-bigtreetech-ebb-sb-canbus-v1.0.cfg

├── EBB SB2209 CAN (RP2040)/           # RP2040-based version
│   ├── Hardware/
│   ├── 3D/
│   ├── Build Guide/                   # PDF manuals
│   └── sample-bigtreetech-ebb-sb-rp2040-canbus-v1.0.cfg

├── Images/                             # Documentation images
├── README.md                           # Main documentation (English)
└── README_zh_cn.md                    # Chinese documentation
```

## Board Versions Reference

### Quick Version Guide

| Board Version | MCU | CAN Bus | Stepper Driver | Key Notes |
|--------------|-----|---------|----------------|-----------|
| EBB36/42 CAN V1.0 | STM32F072 | CAN (PB8/PB9) | TMC2209 | First generation, 8MHz crystal |
| EBB36/42 CAN V1.1 | STM32G0B1 | FDCAN (PB0/PB1) | TMC2209 | Hotend on PA2, **DFU warning** |
| EBB36/42 CAN V1.2 | STM32G0B1 | FDCAN (PB0/PB1) | TMC2209 | Hotend moved to PB13 (safer DFU) |
| EBB SB2209 CAN | STM32G0B1 | FDCAN (PB0/PB1) | TMC2209 | StealthBurner compatible |
| EBB SB2240 CAN | STM32G0B1 | FDCAN (PB0/PB1) | TMC2240 SPI | StealthBurner compatible, advanced driver |
| EBB SB2209 CAN (RP2040) | RP2040 | CAN | TMC2209 | Alternative RP2040-based design |

### Shared Hardware Features (All Versions)

- **Onboard Accelerometer**: ADXL345
- **Temperature Sensing**: Max31865 (PT100/PT1000), 100K NTC thermistor
- **Input Voltage**: DC 12V-24V
- **Heating**: E0 hotend output, 5A max
- **Fans**: 2-3 fan outputs (1A max, 1.5A peak)
- **Interfaces**: USB-C, CAN, I2C, RGB, endstop, probe
- **5V Output**: 1A maximum

## Key Technical Details

### CAN Bus Configuration

**Standard CAN Bus Speed**: 1000000 (1M baud)
All precompiled firmware uses 1M CAN bus speed. This is the standard for Klipper CAN implementations.

### Firmware Build Parameters by Version

#### EBB CAN V1.0 (STM32F072)
```
Micro-controller Architecture: STMicroelectronics STM32
Processor model: STM32F072
Bootloader offset: No bootloader (or 8KiB for CanBoot)
Clock Reference: 8 MHz crystal
Communication interface:
  - USB: USB (on PA11/PA12)
  - CAN: CAN bus (on PB8/PB9)
CAN bus speed: 1000000
```

#### EBB CAN V1.1/V1.2 (STM32G0B1)
```
Micro-controller Architecture: STMicroelectronics STM32
Processor model: STM32G0B1
Bootloader offset: No bootloader (or 8KiB for CanBoot)
Clock Reference: 8 MHz crystal
Communication interface:
  - USB: USB (on PA11/PA12)
  - CAN: CAN bus (on PB0/PB1)
CAN bus speed: 1000000
```

#### Pin Differences V1.1 vs V1.2
- **V1.1**: Hotend heater on PA2
- **V1.2**: Hotend heater on PB13 (only difference)

### Klipper Configuration Files

Each board version has a corresponding `sample-bigtreetech-ebb-*.cfg` file that includes:
- Correct pin mappings for all features
- MCU serial/canbus_uuid configuration
- Extruder configuration
- TMC stepper driver settings
- Fan configurations
- Optional sensor configurations (ADXL345, BLTouch, RGB, filament sensors)

## Critical Safety Warnings

### STM32G0B1 DFU Mode Hotend Hazard (V1.1 ONLY)

**CRITICAL SAFETY ISSUE FOR EBB CAN V1.1**:

When updating firmware via USB DFU mode on V1.1 boards, the STM32G0B1 bootloader configures PA2 as HIGH during the DFU process. Since PA2 controls the hotend heater on V1.1, this causes the hotend to heat uncontrollably.

**Safety Procedures for V1.1**:
1. **ALWAYS disconnect main power (VIN) to the hotend before entering DFU mode**
2. Complete firmware updates quickly to minimize time in DFU mode
3. **NEVER leave the board in DFU mode with hotend power connected**

**V1.2 Fix**: Hotend moved to PB13, eliminating this safety issue.

Reference: AN2606 Application Note, STM32G0B1CB Datasheet

## Development Workflows

### Adding New Firmware

When adding precompiled firmware:

1. **Naming Convention**:
   - `firmware_USB.bin` - USB communication
   - `firmware_canbus.bin` - CAN bus communication (1M baud)
   - `firmware_canbus_8k_bootloader.bin` - CAN with CanBoot support

2. **Location**: Place in the board version's root directory (e.g., `EBB CAN V1.1 (STM32G0B1)/`)

3. **Documentation**: Update the corresponding README section with:
   - Klipper commit hash used
   - Build configuration parameters
   - Date of compilation

### Adding Hardware Revisions

1. **Create subdirectory** under appropriate version folder
2. **Include**:
   - `Hardware/` - Schematics, BOM, pinout PNGs
   - `3D/` - STL files for mounting brackets
   - Klipper config file at version root
3. **Update README.md** with new pinout images and specifications

### Modifying Klipper Configurations

1. **Test thoroughly** before committing
2. **Preserve commented examples** for alternative configurations (e.g., TMC2209 vs TMC2240)
3. **Use correct pin prefixes**: `EBBCan:` for all pins
4. **Include all optional features** as commented examples

### Documentation Updates

- **Both languages**: Update both `README.md` (English) and `README_zh_cn.md` (Chinese)
- **Images**: Store in `Images/` directory, reference with relative paths
- **Pinout diagrams**: PNG format, minimum 800px width
- **Build guides**: PDF format in `Build Guide/` subdirectory

## File Naming Conventions

### Firmware Files
- Lowercase, descriptive: `firmware_canbus.bin`
- Indicate special configs: `firmware_canbus_8k_bootloader.bin`

### Configuration Files
- Pattern: `sample-bigtreetech-ebb-{variant}-{version}.cfg`
- Examples:
  - `sample-bigtreetech-ebb-canbus-v1.0.cfg`
  - `sample-bigtreetech-ebb-sb-canbus-v1.0.cfg`
  - `sample-bigtreetech-ebb-sb-rp2040-canbus-v1.0.cfg`

### Hardware Files
- Schematics: Board name + version (e.g., `EBB36 CAN V1.1-SCH.pdf`)
- Pinout: Board name + version + `-PIN.png`
- BOM: Board name + version + `-BOM.xlsx`

### 3D Print Files
- STL format for distribution
- STEP format for CAD sources (in `CAD/` directory)
- Descriptive names: `Cable_Cover_For_PCB_V1.2.stl`

## Common Maintenance Tasks

### Updating Firmware Binaries

1. Set up Klipper build environment on Raspberry Pi/host
2. Configure menuconfig for target board
3. Run `make` to compile
4. Transfer `klipper.bin` from `~/klipper/out/`
5. Rename to appropriate convention
6. Update documentation with commit hash
7. Commit both firmware and documentation updates

### Adding New Board Variant

1. Create directory structure matching existing boards
2. Generate pinout diagram (800px+ width PNG)
3. Create Klipper config file with all pin mappings
4. Write build guide if significantly different
5. Update main README.md with new section
6. Include hardware files (schematics, BOM)

### Testing Klipper Configurations

Before committing config changes:
1. Verify all pin assignments against schematic
2. Test communication (USB or CAN)
3. Verify TMC UART/SPI communication
4. Test ADXL345 if configured
5. Check temperature sensor readings
6. Verify fan control
7. Document any special requirements

## Integration with Voron/StealthBurner

### StealthBurner Compatibility

The SB2209 and SB2240 boards are designed for Voron StealthBurner toolheads:
- Custom printed parts provided in `STL/` directories
- Compatible with official StealthBurner parts except cable covers
- See `readme.md` in SB2240_2209 CAN directory for part details
- Original StealthBurner files: https://github.com/VoronDesign/Voron-Stealthburner

### Custom Printed Parts

- `main_body_EBB` - Modified main body for EBB boards
- `Cable_Cover_For_PCB` - Board-specific cable cover
- `Printed_Part_for_CAN_Cable` - CAN cable routing
- Users may need to modify USB-C cable parts for their specific connectors

## AI Assistant Best Practices

### When Analyzing Issues

1. **Identify board version first** - Pin assignments vary between versions
2. **Check MCU type** - Build parameters differ (STM32F072 vs STM32G0B1 vs RP2040)
3. **Verify CAN bus pins** - Changed between V1.0 (PB8/PB9) and V1.1+ (PB0/PB1)
4. **Note hotend pin** - Critical difference between V1.1 (PA2) and V1.2 (PB13)

### When Providing Firmware Build Instructions

1. Always specify exact MCU model
2. Include clock reference (8 MHz crystal for all STM32 variants)
3. Warn about DFU safety for V1.1 boards
4. Specify correct CAN pins for version
5. Mention standard 1M CAN bus speed

### When Editing Configurations

1. **Never remove commented examples** - Users need alternatives
2. **Preserve pin prefix format** - `EBBCan:` is required
3. **Test critical sections** - Extruder, heater, thermistor configs
4. **Maintain backward compatibility** - Don't break existing user configs

### When Adding Documentation

1. **Use relative paths** for images
2. **Maintain bilingual parity** - Update both EN and ZH docs
3. **Include visual references** - Pinout diagrams essential
4. **Link to official resources** - Klipper docs, STM datasheets

### What NOT to Do

- **Never guess pin assignments** - Always verify against hardware files
- **Don't mix V1.1 and V1.2 configs** - Hotend pin differs
- **Don't ignore safety warnings** - DFU hazard is critical for V1.1
- **Don't modify precompiled firmware** - Document build params instead
- **Don't delete old board versions** - Users still use V1.0 boards

## External References

### Official Resources
- **Klipper Documentation**: https://www.klipper3d.org/
- **Klipper GitHub**: https://github.com/Klipper3d/klipper
- **Voron StealthBurner**: https://github.com/VoronDesign/Voron-Stealthburner
- **BigTreeTech Site**: https://bigtree-tech.com/

### Technical References
- **STM32F072 Reference**: STMicroelectronics datasheets
- **STM32G0B1 Reference**: STMicroelectronics datasheets, AN2606
- **RP2040 Reference**: Raspberry Pi Pico documentation
- **TMC2209 Driver**: Trinamic datasheets
- **TMC2240 Driver**: Trinamic datasheets

### Purchase and Support
- **Store**: https://www.biqu.equipment/
- **Support Email**: service004@biqu3d.com
- **Community**: Facebook, Twitter, Instagram (see README)

## Version History Tracking

When making changes, note:
- **Last firmware update**: Check commit messages for Klipper source version
- **Hardware revisions**: V1.0 → V1.1 → V1.2 progression
- **Config file updates**: Match to hardware and firmware versions
- **3D printed part versions**: Track STL file revisions (e.g., V1.1, V1.2)

---

**Document Version**: 1.0
**Last Updated**: 2025-12-22
**Maintainer**: BigTreeTech / Community Contributors
