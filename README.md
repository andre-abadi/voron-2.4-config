# Introduction

Voron V2.4071 software configuration and hardware configuration (via this document).

# Serial
- Serial `V2.4071`
    - [Serial Request Link](https://www.reddit.com/r/voroncorexy/comments/woqokq/serial_request_for_ldo_voron_v24r2_300_by/)
    - Serial obtained on Wednesday 17 August 2022 at 1:15pm UTC+8
- LDO Kit Serial `V2.2205160434`

# Build

- [Voron 2.4](https://vorondesign.com/voron2.4)
- [LDO 2.4r2 Kit 300mm Red](https://docs.ldomotors.com/en/voron/voron2)
- [PIF](https://pif.voron.dev/)
    - eSun ABS+ Black
- [Phaetus Dragon HF Hotend](https://www.phaetus.com/dragon-hotend-hf/)
- Additional parts in eSun ABS+ Black and generic ABS Black

# Config

## [Octopus Pro](https://docs.vorondesign.com/build/software/octopus_klipper.html)

|Menuconfig|Setting|
|---|---|
|Enable extra low-level configuration options|`*`|
|Micro-controller Architecture|`STMicroelectronics STM32`|
|Processor model|`STM32F446`|
|Bootloader offse|`32KiB bootloader`|
|Clock Reference|`12 MHz crystal`|
|Communication interface|`USB (on PA11/PA12)`|
|USB ids|`--->`|
|Specify a custom step pulse duration|`[ ]`|
|GPIO pins set at micro-controller startup|`()`|

### Verified Update Steps

- `cd ~/klipper`
- `make clean`
- `make menuconfig`
    - Double check it matches the above table.
- `q` (quit)
- `y` (save)
- `make`
- `cp -f out/klipper.bin ~/klipper_config/firmware.bin`
    - Overwrites any previous `firmware.bin`
    - `klipper.bin` won't be recognised by the board, it has to be `firmware.bin` which is why this copy command also renames it.
- Download `firmware.bin` via MainsailOS/Machine.
- Check MicroSD card is `FAT32`.
- Copy `firmware.bin` to root of MicroSD card.
- Power off printer (to power off board completely).
- Insert MicroSD card into board or header ribbon.
- `ls /dev/serial/by-id`
    - Should show the board connected with Klipper.
- Successful flash is also indicated by MicroSD card containing single file `FIRMWARE.CUR`

## [Nitehawk USB Toolhead PCB](https://docs.ldomotors.com/voron/nitehawk-sb)

|Menuconfig|Setting|
|---|---|
|Enable extra low-level configuration options|`*`|
|Micro-controller Architecture|`Raspberry Pi RP2040`|
|Bootloader offse|`16KiB bootloader`|
|Communication interface|`USB`|
|USB ids|`--->`|
|GPIO pins set at micro-controller startup|`!gpio8`|

### Verified Update Steps

- `ls /dev/serial/by-id`
- `cd ~/klipper`
- `make clean`
- `make menuconfig`
    - Double check it matches the above table.
- `q` (quit)
- `y` (save)
- `make`
- `sudo apt install python3 python3-pip`
    - Idempotent; won't hurt if already installed.
- `pip3 install pyserial`
    - LDO docs say `pip` but `pip3` worked and `pip` did not.
- `cd ~/klipper`
- `sudo service klipper stop`
- `make flash FLASH_DEVICE=/dev/serial/by-id/usb-Klipper_rp2040_3033393834049F97-if00`
    - This will throw an error but results in the board restarting into the bootloader mode (Katapult) in the below `by-id`.
- `make flash FLASH_DEVICE=/dev/serial/by-id/usb-katapult_rp2040_3033393834049F97-if00`
    - This should work and result in the board restarting into Klipper mode.
- `ls /dev/serial/by-id`
    - This should show the board back into Klipper mode.
- `sudo service klipper start`
    - In Nitehawk should now be at same version as host as seen on MainsailOS.

# Software
- PrusaSlicer
    - [Build Plate & Texture](https://github.com/hartk1213/MISC/tree/main/Voron%20Mods/SuperSlicer/Bed_Models/V2)
- Extra Subroutines
    - [Heatsoak](https://github.com/MakerBogans/KlipperConfig/blob/master/Verethy/VT-627/macros.cfg) `heatsoak.cfg`
    - [Nozzle Scrub](https://www.youtube.com/watch?v=ZEXYnXZA8B0&t=3s) `clean_nozzle.cfg`
    - LCD Macro overrides and extra chamber temp on LCD `lcd-macros.cfg`
    - Temperature Fan `temperature_fan.cfg`
    - Stealthburner LEDs including custom for cooldown `stealthburner_leds.cfg`
    - Auto Z Calibration `z_calibration.cfg` **

# Mods

## Hardware

- [LDO Build](https://docs.ldomotors.com/en/voron/voron2/printed_part_guide_rev_a)
    - [LDO Door Latches](https://github.com/MotorDynamicsLab/LDOVoron2/tree/main/STLs/LDO%20Door)
    - [LED Clips](https://github.com/VoronDesign/VoronUsers/blob/master/printer_mods/eddie/LED_Bar_Clip/LED_Bar_Clip_Misumi_version2.stl)
    - [Beefy Raspberry Pi Mount](https://github.com/MotorDynamicsLab/LDOVoron2/blob/main/STLs/beefy_raspberry_bracket.stl)
    - Z Rail Stoppers
- [KlickyNG](https://github.com/jlas1/Klicky-Probe)
- [Stealthburner RC1](https://github.com/VoronDesign/Voron-Stealthburner)
- [Removable Doors](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/ElPoPo/RemovableDoors)
    - First iteration used VHB tape
    - Have since replaced VHB option with screw option
- [Panel Latches](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/richardjm/snap-latch-2020)
    - 4mm for Top and Back Panels
    - 7mmm for Sides
- [Horizontal Spool Holder](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/BladeScraper-Designs/Horizontal-Spool-Holder)
- [Nozzle Scrubber with a Little Bucket for Voron 2.4](https://www.printables.com/model/201999-nozzle-scrubber-with-a-little-bucket-for-voron-24)
    - Note this does have an issue with the STL where there is a gap in the design, it prints OK but with artifacts, author yet to rectify
- [Deck Panel Support Clips](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/wile-e1/Deck_Panel_Support_Clips)
    - Lower 4mm
- [Corner Cable Hide / Cover (LED Wires)](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/samwiseg0/corner_cable_hide)
  - Right version used (see below for remix on left side)
- [Exhaust Cover](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/Fiction/Exhaust_cover)
- [CW1 Stealthburner PCB Cover](https://github.com/hartk1213/MISC/blob/main/Voron%20Mods/Extruders/CW1/STLs/sb_pcb_cover.stl)
- [Nevermore V5 Micro](https://github.com/nevermore3d/Nevermore_Micro)
    - Using [Ellis's Bed Fan Macros](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/Ellis/Bed_Fans)
- [Angry Cam](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/chri.kai.in/Angry_CAM_USB)
- [MicroSD skirt headers for MCU and Rpi](https://www.thingiverse.com/thing:5036693)
- [Killstractor](https://github.com/MakerBogans/BoganParts/tree/main/killcode/Killstractor)
- [BTT Smart Filament Sensor and Mount](https://www.printables.com/model/220585-voron-v2-btt-smart-filament-sensor-mount)
- [Titanium Gantry Backers](https://github.com/tanaes/whopping_Voron_mods/tree/main/extrusion_backers)
- [Bed_Fans](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/Ellis/Bed_Fans)
- CW2/TAP Upgrade
    - [CW2 from SB](https://github.com/VoronDesign/Voron-Stealthburner)
    - [TAP](https://github.com/VoronDesign/Voron-Tap)
- [Nitehawk USB Toolhead PCB](https://docs.ldomotors.com/voron/nitehawk-sb)

## Remixed Hardware Mods
- [LRS200 Door Cover](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/samwiseg0/lrs_screw_terminal_cover)
    - Made outer edges thinner to fit in my LRS200
- [Corner Cable Hide / Cover (LED Wires)](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/samwiseg0/corner_cable_hide)
    - `Tallboi` is made 50% taller so that LDO LED PCB can be mounted inside
- [LDO LED PCB Spacer](https://github.com/MotorDynamicsLab/LDOVoron2/blob/main/STLs/led_pcb_spacer.stl)
    - `Thinboi` is made 20% narrower to fit inside `Tallboi` above
- [LDO Handlebar Spacer](https://github.com/MotorDynamicsLab/LDOVoron2/blob/main/STLs/handlebar_spacer_x4.stl)
    - Made extrusion spacers
        - 6mm deep so as to fill entire depth space
        - 6mm thick to fill entire width space
    - Made body protrude by 7mm so that 3mm acrylic and 3mm foam panel can fit underneath
    - So that can be used by handles placed vertically on left and right of the printer
        - Must be printed at 100% infill so that handlebars can be hulked into place
- [Removable Doors](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/ElPoPo/RemovableDoors)
    - Remixed hinges to have indexing holes so easier to put doors back on

# Unused
Things no longer used on this printer.

## Build
- [PIF](https://pif.voron.dev/)
    - AmazonBasics ABS Red

## Software
- SuperSlicer
    - [Build Plate & Texture](https://github.com/hartk1213/MISC/tree/main/Voron%20Mods/SuperSlicer/Bed_Models/V2)
- Extra Subroutines
    - [Autocommit](https://github.com/th33xitus/kiauh/wiki/How-to-autocommit-config-changes-to-github%3F) `autocommit.cfg` + `autocommit.sh`
    - Auto Z Calibration `z_calibration.cfg` **

## Plans
- [ADXL skirt header](https://www.thingiverse.com/thing:5026196)
- [Auto Z calibration](https://github.com/protoloft/klipper_z_calibration)
- [Frame Expansion Sensor](https://www.klipper3d.org/Config_Reference.html#z_thermal_adjust)


