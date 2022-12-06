# Introduction

Voron V2.4071 software configuration and hardware configuration (via this document).

# Build

- [Voron 2.4](https://vorondesign.com/voron2.4)
- [LDO 2.4r2 Kit 300mm Red](https://docs.ldomotors.com/en/voron/voron2)
- [PIF](https://pif.voron.dev/)
    - eSun ABS+ Black
    - AmazonBasics ABS Red
- [Phaetus Dragon HF Hotend](https://www.phaetus.com/dragon-hotend-hf/)
- Additional parts in eSun ABS+ Black

# Config
- [Octopus Pro](https://docs.vorondesign.com/build/software/octopus_klipper.html)
  - `STMicroelectronics STM32`
  - `STM32F446`
  - `32KiB bootloader`
  - `12 MHz crystal` (for STM32F446)

# Software
- SuperSlicer
    - [Build Plate & Texture](https://github.com/hartk1213/MISC/tree/main/Voron%20Mods/SuperSlicer/Bed_Models/V2)
- Extra Subroutines
    - [Heatsoak](https://github.com/MakerBogans/KlipperConfig/blob/master/Verethy/VT-627/macros.cfg) `heatsoak.cfg`
    - [Autocommit](https://github.com/th33xitus/kiauh/wiki/How-to-autocommit-config-changes-to-github%3F) `autocommit.cfg` + `autocommit.sh`
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

# Plans
- [ADXL skirt header](https://www.thingiverse.com/thing:5026196)
- [Auto Z calibration](https://github.com/protoloft/klipper_z_calibration)
- [Frame Expansion Sensor](https://www.klipper3d.org/Config_Reference.html#z_thermal_adjust)
- CW2/TAP Upgrade
    - [CW2 from SB](https://github.com/VoronDesign/Voron-Stealthburner)
    - [TAP](https://github.com/VoronDesign/Voron-Tap)
    - [LDO Toolhead PCB and Cover](https://www.ldomotion.com/p/guide/18295873486193741)
