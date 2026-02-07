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

## [Octopus Pro v1.0.1 STM32F446](https://docs.vorondesign.com/build/software/octopus_klipper.html)

|Menuconfig|Setting| 2nd Level Setting |
|---|---|---|
|Enable extra low-level configuration options|`*`||
|Micro-controller Architecture|`STMicroelectronics STM32`||
|Processor model|`STM32F446`||
|Bootloader offse|`32KiB bootloader`||
|Clock Reference|`12 MHz crystal`||
|Communication interface|`USB (on PA11/PA12)`||
|USB ids|`--->`||
|| USB vendor ID | `(0x1d50)` |
|| USB device ID | `(0x614e)` |
|| USB serial number from CHIPID | `[*]` |
| Optimize stepper code for 'step on both edges |`[*]`||
| GPIO pins set at micro-controller startup |`()`||

### Verified Update Steps

- `cd ~/klipper`
- `make clean`
- `make menuconfig`
    - Double check it matches the above table.
- `q` (quit)
- `y` (save)
- `make`
- `cp -f out/klipper.bin ~/printer_data/config/firmware.bin`
    - Overwrites any previous `firmware.bin`
    - `klipper.bin` won't be recognised by the board, it has to be `firmware.bin` which is why this copy command also renames it.
- Download `firmware.bin` via MainsailOS/Machine.
- Check MicroSD card is `FAT32`.
- Copy `firmware.bin` to root of MicroSD card.
- Power off printer (to power off board completely).
- Insert MicroSD card into board or header ribbon.
- Power on printer and check MCU Klipper version in Mainsail
- Alternatively:
    - `ls /dev/serial/by-id`
        - Should show the board connected with Klipper.
    - Successful flash is also indicated by MicroSD card containing single file `FIRMWARE.CUR`

## [Nitehawk USB Toolhead PCB](https://docs.ldomotors.com/voron/nitehawk-sb)

|Menuconfig|Setting| 2nd Level Setting|
|---|---|---|
|Enable extra low-level configuration options|`*`||
|Micro-controller Architecture|`Raspberry Pi RP2040`||
|Processor model|`rp2040`||
|Bootloader offse|`16KiB bootloader`||
|Communication interface|`USBSERIAL`||
|USB ids|`--->`||
|| USB vendor ID | `(0x1d50)` |
|| USB device ID | `(0x614e)` |
|| `*` | USB serial from CHIPID (NEW) |
| Optimize stepper code for 'step on both edges |`[*]`||
|GPIO pins set at micro-controller startup|`!gpio8`||

### Verified Update Steps

- `sudo apt install python3 python3-pip python3-serial`
    - Idempotent; won't hurt if already installed.
- `cd ~/klipper`
- `make clean`
- `make menuconfig`
    - Double check it matches the above table.
- `q` (quit)
- `y` (save)
- `make`
- `ls /dev/serial/by-id`
- `cd ~/klipper`
- `sudo service klipper stop`
- `make flash FLASH_DEVICE=/dev/serial/by-id/usb-Klipper_rp2040_3033393834049F97-if00`
    - Upgrading Klipper 0.12 to 0.13 this worked successfully using the above without needing to manually switch to bootloader mode.
    - This used to throw an error but make the board reboot in usb-Katapult bootloader in the below `by-id`.
- `sudo service klipper start`
    - In Nitehawk should now be at same version as host as seen on MainsailOS.

## [Input Shaper Auto Calibration](https://www.klipper3d.org/Measuring_Resonances.html#input-shaper-auto-calibration)

- `STARTUP_SELF_TEST`
    - Just to turn on the lights and get the printer ready.
- `ACCELEROMETER_QUERY`
    - Should give readings on three axes to show that the accelerometer is ready.
- `SHAPER_CALIBRATE`
    - Does everything automatically.
    - Take note the max acceleration recommended for below.
- To calculate [`max_accel`](https://www.klipper3d.org/Measuring_Resonances.html#selecting-max_accel)c for each of `shaper_type_x` and `shaper_type_y` in the results of `SHAPER_CALIBRATE`:
    - Look up that shaper name.
    - Verify a match of frequencies between instances of that shaper name for that axis.
    - Take note of suggested `max_accel`
- `SAVE_CONFIG`
- In `printer.cfg` update `[max_accel]`
    - Lowest of the two.
    - Safety margin, recommend rounding down to nearest `500mm/sec^2`

## Raspberry Pi 4+ 4GB
- [MainsailOS](https://docs-os.mainsail.xyz/getting-started/raspberry-pi-os-based) via [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
- [Klipperscreen](https://klipperscreen.readthedocs.io/en/latest/):
    - [KIAUH](https://github.com/dw-0/kiauh) to install 
    - Rotate PiTFT 4.3 by 180 degrees using [instructions](https://klipperscreen.readthedocs.io/en/latest/Troubleshooting/Rotation/)
        - `console=tty1 root=PARTUUID=e6106aed-02 rootfstype=ext4 fsck.repair=yes rootwait cfg80211.ieee80211_regdom=EG video=[0/0]:800x480@60,rotate=180`
    - Rotate touch interface by 180 degrees by editing `/etc/udev/rules.d/51-touchscreen.rules`
        - `ACTION=="add", ATTRS{name}=="10-0038 generic ft5x06 (00)", ENV{LIBINPUT_CALIBRATION_MATRIX}="-1 0 1  0 -1 1  0 0 1"`

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
- [Removable Doors](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/ElPoPo/RemovableDoors)
    - First iteration used VHB tape
    - Have since replaced VHB option with screw option
- [Panel Latches](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/richardjm/snap-latch-2020)
    - 4mm for Top and Back Panels
    - 7mmm for Sides
- [Nozzle Scrubber with a Little Bucket for Voron 2.4](https://www.printables.com/model/201999-nozzle-scrubber-with-a-little-bucket-for-voron-24)
    - Note this does have an issue with the STL where there is a gap in the design, it prints OK but with artifacts, author yet to rectify
- [Deck Panel Support Clips](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/wile-e1/Deck_Panel_Support_Clips)
    - Lower 4mm
- [Corner Cable Hide / Cover (LED Wires)](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/samwiseg0/corner_cable_hide)
  - Right version used (see below for remix on left side)
- [Exhaust Cover](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/Fiction/Exhaust_cover)
- [Angry Cam](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/chri.kai.in/Angry_CAM_USB)
- [MicroSD skirt headers for MCU and Rpi](https://www.thingiverse.com/thing:5036693)
- [Killstractor](https://github.com/MakerBogans/BoganParts/tree/main/killcode/Killstractor)
- [Titanium Gantry Backers](https://github.com/tanaes/whopping_Voron_mods/tree/main/extrusion_backers)
- [Bed_Fans](https://github.com/VoronDesign/VoronUsers/tree/master/printer_mods/Ellis/Bed_Fans)
- CW2/TAP Upgrade
    - [CW2 from SB](https://github.com/VoronDesign/Voron-Stealthburner)
    - [TAP](https://github.com/VoronDesign/Voron-Tap)
- [Nitehawk USB Toolhead PCB](https://docs.ldomotors.com/voron/nitehawk-sb)
- [BTT SFS 2.0 Filament Motion & Runout Sensor](https://bigtree-tech.com/blogs/news/new-release-bigtreetech-sfs-v2-0)
    - Mounted with [Voron BTT Smart Filament Sensor (SFS)2.0 Mount by Tom](https://www.printables.com/model/568619-voron-btt-smart-filament-sensor-sfs20-mount-by-tom)
- [BTT PI TFT43](https://store.dremc.com.au/products/btt-pitft50-v2-0-with-5-inch-touch-screen-2021-version_
    - Mounted with [Voron BigTreeTech 4.3" PI TFT Mount by MandicReally](https://thangs.com/designer/MandicReally/3d-model/Voron%20BigTreeTech%204.3%22%20PI%20TFT%20Mount%20-%20Hex%20%26%20LDO%20designs%21-851510)

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
