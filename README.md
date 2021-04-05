# Vulcanus Max

This is the definitive repository for config files for this printer. Firmware is not stored here!

Vulcanus Max is an open source CoreXY, large-capacity printer:
* 2020 extrusion frame
* NEMA 17 stepper motors
	* Kysan 1124090: 1.5A, 4.2V, 1.8 deg
	* Bondtech Extruder
* BigTree Tech SKR v1.4 (LPC1768-based)
* BigTree Tech TFT35 V3.0 Graphic Smart Display Controller https://smile.amazon.com/gp/product/B07VWF4W3J/ref=ppx_yo_dt_b_search_asin_title?ie=UTF8&psc=1
* TMC2209 Drivers
	* Sensorless homing (X&Y)
* BuildTak flexible build plate
* Dual E3D V6 hot ends
* Type 1 thermistor: HT-NTC100K 3950

# Configure and Build Marlin

* Use VSCode. 
* Download latest Marlin, if you so choose.
* Copy latest config from source/Marlin in this repo. 
	* Note: often best to transfer values to new config so as to be up to date on Marlin parameters.

## PINDA Probe Wiring

Connect to Z-MIN. Note: Vulcanus is wired to the Probe and Sensor, which is entirely unnecessary and should be changed to Z-MIN (2/28/21)
bl red green black
+---+
  . | ----- +5V ----- Black -------- Brown
| . | ----- SIG ----- Red   -------- Black 
  . | ----- GND ----- Green -------- Blue 
+---+    

### Pinda wires

* W - not used (temperature sensor on Prusa printers)
* Black - SIG      
* Blue - GND
* Brown - +5V

## Wifi Support

If connecting for the first time, stop looking all over the internet and worrying with that silly BTT Writer. Look at available WiFi SIDs. 

1) Connect to ESP3D. 
2) Go to 192.168.0.1
3) Upload index.html.gz
4) Reboot the printer
5) Go to 192.168.0.1
6) Follow the setup instructions

After initial configuration you'll need to reboot the printer to get the DHCP address from the display. Navigate to that IP and continue setup.

7) Change Station IP Mode to Static
8) Set an IP address (use OctoPi + 1)
9) Set gateway IP
10) Reboot the printer when done and verify static IP address is set

## Embedded WebUI

First time you will need to upload index.html.gz. Get it from https://github.com/luc-github/ESP3D-WEBUI. Use the default UI to upload it, then reboot.


## Notes

**Extruder**: Do not exceed 30mm/s extraction speed. It's a geared extruder and it *does not like it.*

# Build

* Build with PlatformIO. Easiest to output to SD Card

# Install

* Put the MicroSD card from **Build** in the printer and power off/on.
* Reset the EEPROM stored settings: M502, M500, M501

# Operate

## UBL

**Flex Plate**: UBL works very well with the BuildTak flexible build plate system. A script has been configured on Octopi for this purpose.

**Note on Sensorless Probing:** I read that it's not that accurate enough on the Z-axis. TMC2209 on the E0, E1, and Z axis have Stallguard diagnostic pins removed.

Latest: 2/28/21

```
;------------------------------------------
;--- Setup and initial probing commands ---
;------------------------------------------
;M502            ; Reset settings to configuration defaults...
;M500            ; ...and Save to EEPROM. Use this on a new install.
;M501            ; Read back in the saved EEPROM.
M117 UBL Bed Leveling...
M140 S{{ parameters.bedtemp }}          ; Not required, but having the printer at temperature helps accuracy
M109 S{{ parameters.extrudertemp }}     ; Not required, but having the printer at temperature helps accuracy
M190 S{{ parameters.bedtemp }}          ; Wait for the bed (if not already at temperature)

G28             ; Home XYZ.
M155 S30        ; reduce temperature reporting rate
G29 P1 T        ; Do automated probing of the bed.
;G29 P2 B T      ; Manual probing of locations USUALLY NOT NEEDED!!!!
G29 P3 T        ; Repeat until all mesh points are filled in.

#G29 T           ; View the Z compensation values.
G29 S{{ parameters.slot }} ; Save UBL mesh points to EEPROM.
G29 F{{ parameters.fade }}; Set Fade Height for correction at 10.0 mm.
G29 A           ; Activate the UBL System.
@BEDLEVELVISUALIZER     ; tell plugin to watch for reported mesh
M420 S1 V
M500            ; Save current setup. WARNING: UBL will be active at power up, before any `G28`.
;---------------------------------------------
;--- Fine Tuning of the mesh happens below ---
;---------------------------------------------
;G26 C P5.0 F3.0 ; Produce mesh validation pattern with primed nozzle (5mm) and filament diameter 3mm
                ; PLA temperatures are assumed unless you specify, e.g., B 105 H 225 for ABS Plastic
;G29 P4 T        ; Move nozzle to 'bad' areas and fine tune the values if needed
                ; Repeat G26 and G29 P4 T  commands as needed.

;G29 S1          ; Save UBL mesh values to EEPROM.
;M500            ; Resave UBL's state information.
;----------------------------------------------------
;--- Use 3-point probe to transform a stored mesh ---
;----------------------------------------------------
;G29 L1          ; Load the mesh stored in slot 1 (from G29 S1)
;G29 J           ; No size specified on the J option tells G29 to probe the specified 3 points
                ; and tilt the mesh according to what it finds.

M104 S0
M140 S0
M155 S30		; reset temperature keepalive
```

### Setup

#### Do this any time a change is made to the build surface that could change its contour. Run the script above manually, or use the custom command in Octopi.

#### Step 2 - Glass beds

Manually adjust every point on the mesh with a feeler gauge. From the clickwheel, find the menu item to adjust every mesh point. Adjust each to consistent clearance.

#### Step 3

Optional: Get the mesh, load it into Excel, and create a 3D graph of the surface.

1. Copy/paste the mesh data from G29 T
2. Paste into Excel (see file in this repo)
3. Data->Text to columns. Take defaults
4. Delete columns 1 and 3
5. Select data cells and sort by column A (gets rid of blanks and orients graph to match front right view of the bed)
6. Create a 3D Line Chart of the bed measurements

![UBL Bed Topology Visualized as an Excel 3D Line Chart](bed-topo.png)

## Bilinear Bed Leveling
### Procedure
1) Preheat for PLA. Heaters (bed and hot end) should remain on. Let the bed fully soak in the heat for a few minutes
2) Use the menu to execute Bed Leveling (I think this is G29)
3) Make sure Bed Leveling is ON

## Adjustments
### Z Offset
* Adjust it before you print
* ...or double-click the knob **during** print and adjust (head adjusts as you turn the knob)
# Contents of this Repo

## source/Marlin

Contains Marlin configs.

### To Update Marlin

* download a zip of the latest Marlin firmware
* Copy the file platformio.ini to the root (same level as Marlin, docs, data, lib, etc)
* Copy the latest configs to the Marlin folder within the downloaded repo
* Build using VSCode

**Note**: It's better to transcribe your config, editing the latest Marlin version to match. Often config directives change between releases. Same for platformio.


Latest build is in Documents/Marlin/Marlin-bugfix-2.0.x, built 4/23/2020. I was attempting to make UBL work properly. It didn't. See open issue on GitHub. Working currently on adding WiFi support.

WiFi is enabled on Serial 1

### See:
* http://www.github.com/codiac2600

## Cura

All files needed to install the printer into Cura

## Other

printer_init.gcode - sets up the unified bed leveling mesh

# Specifications

https://mak3r.de/2020/04/03/stepper-driver-current-and-vref-calculator/

## X Axis

Stepper motor: Kysan 1124090 Nema 17
2 Phase
1.8 angle
2.8ohm / phase
1.5A / phase
5mm

RMS = 1.5 / 1.41 = 1.064
Vref = (1.064 * 2.5) / 1.77 = 1.502
90% = 1.5 = 958mA

Driver: TMC2209 in UART mode with StallGuard

Pulley: GT2 20 tooth

Belt pitch: 2mm

## Y Axis

Stepper motor: Kysan 1124090 Nema 17
2 Phase
1.8 angle
2.8ohm / phase
1.5A / phase
5mm

RMS = 1.5 / 1.41 = 1.064
Vref = (1.064 * 2.5) / 1.77 = 1.502
90% = 1.5 = 958mA

Driver: TMC2209 in UART mode with StallGuard

Pulley: GT2 20 tooth

Belt pitch: 2mm

## Z Axis

Stepper motor: Kysan 1124090 Nema 17 (2)
2 Phase
1.8 angle
2.8ohm / phase
1.5A / phase
5mm

RMS = 2 * 1.5 / 1.41 = 2.128
Vref = (2.128 * 2.5) / 1.77 = 3.006
90% = 1.9152 = 1915mA

RMS: Conservatively set X_CURRENT to 1500 (2/28/21)

Driver: TMC2209 in UART mode

Min Z Probe: 18mm inductive

Lead screw: 8mm

## Extruder 1

Stepper motor: Bondtech 42H025H-0704-002 25mm NEMA 17 0.7A, 2.9V, 1.8 deg
2 Phase
1.8 angle
4.2ohm / phase
0.7A / phase
2.9V / phase

Driver: TMC2209 in UART mode

RMS = 0.7 / 1.41 = 0.496
Vref = (0.496 * 2.5) / 1.77 = 0.700 V
90% = 0.496 = 446mA

## VREF Calculation

RMS current = Max current / 1.41
Vref = (RMS Current x 2.5) / 1.77
Use 90% of RMS current

----- old stuff----
# Vulcanus Max
## Overview

This project is to create a large-format 300x300x400mm Fused Deposit Modelling (FDM) printer based on CoreXY design using the 2015 Vulcanus Max project as a starting point.

## Configure and Build Marlin

1) Download BTT Marlin 2.0.x bugfix
2) Download latest 2.0 Marlin
3) Copy #2 over #1
4) Copy platformio.ini to root of #1 over the exiting platformio.ini
5) Edit the config files in #1 and transfer settings from latest build/Marlin in this repo

### Specific Config Changes

#### configuration.h

Numerous (to be listed)

```
#define STRING_CONFIG_H_AUTHOR "(Elvantech, SKR-V1.4 LPC1768)"
#define SERIAL_PORT 3
#define SERIAL_PORT -1
#define BAUDRATE 115200
#define MOTHERBOARD BOARD_BTT_SKR_V1_4
#define CUSTOM_MACHINE_NAME "Vulcanus SKR"
#define REPRAP_DISCOUNT_FULL_GRAPHIC_SMART_CONTROLLER
#define NEOPIXEL_LED
  #define NEOPIXEL_TYPE   NEO_GRB 
  #define NEOPIXEL_PIN     P1_24  
  #define NEOPIXEL_PIXELS 26    
  #define NEOPIXEL_STARTUP_TEST 
```
Preheats
```
#define PREHEAT_1_LABEL       "PLA"
#define PREHEAT_1_TEMP_HOTEND 210
#define PREHEAT_1_TEMP_BED     60
#define PREHEAT_1_FAN_SPEED     0 // Value from 0 to 255

#define PREHEAT_2_LABEL       "ABS"
#define PREHEAT_2_TEMP_HOTEND 245
#define PREHEAT_2_TEMP_BED    105
#define PREHEAT_2_FAN_SPEED     0 // Value from 0 to 255

#define PREHEAT_3_LABEL       "Nylon"
#define PREHEAT_3_TEMP_HOTEND 245
#define PREHEAT_3_TEMP_BED     80
#define PREHEAT_3_FAN_SPEED     0 // Value from 0 to 255

#define PREHEAT_4_LABEL       "PETG"
#define PREHEAT_4_TEMP_HOTEND 230
#define PREHEAT_4_TEMP_BED     70
#define PREHEAT_4_FAN_SPEED     0 // Value from 0 to 255

#define PREHEAT_5_LABEL       "TPU"
#define PREHEAT_5_TEMP_HOTEND 200
#define PREHEAT_5_TEMP_BED     90
#define PREHEAT_5_FAN_SPEED     0 // Value from 0 to 255
```


#### configuration_adv.h

X_CURRENT = 1000
Y_CURRENT = 1000
Z_CURRENT = 1000


#### configuration_secure.h

Just copy it.

## Objectives

### Features

* Remote printing

Use OctoPrint to print over WiFi. The built-in camera lets you monitor your print. OctoPrint allows you to remotely control the printer.

* Heated Bed

The build surface can use the Monoprice 300x300 flexible build plate or borosilicate glass plate. Autoleveling makes print setup easier.

* Zoned Heated Bed

The 300x300mm heated bed has 3 zones, small, medium, or large, to reduce power consumption by only heating the portion of the bed that is needed based on the footprint of the model.

* Controller

32 bit processor running at 120MHz with 2209 stepper motor drivers and Marlin 2.0 firmware.

* Extruders

Dual Bowden tube extruders minimize print head mass for less vibration and faster print head movement.

* Vibration-free Frame

Dampers are used on all moving parts to reduce vibration.

* Ambient Temperature Management

Insulated side panels and an exhaust fan allow control of the ambient temperature inside the printer. A heater brings the enclosure quickly up to temperature before your print starts.

* Safety

High amperage power is isolated and protected so that an electrical fault will not cause a fire or damage to the controller.

## Design

### Frame

### Lighting

### Gantry

### Heated Bed

### Print Head

#### Hot Ends

#### Extruders

### Controller

32 bit BIGTREETECH

#### Firmware

Marlin 2.0

### Power

## Bill of Materials

| Item | Qty | Cost |
| -- | :--: | --: |
| [BIGTREETECH TMC2209 V1.2 Stepper Motor Drivers](https://www.aliexpress.com/snapshot/0.html?spm=a2g0s.9042647.6.2.236d4c4dOF5Ewb&orderId=8010661253423936&productId=33029587820)| 5 | $28.88 |
| [BIGTREETECH SKR V1.4 Turbo Control Board](https://www.aliexpress.com/snapshot/0.html?spm=a2g0s.9042647.6.5.236d4c4dOF5Ewb&orderId=8010661253433936&productId=4000480059509)| 1 | $33.00 |
| Stepper Motors | | |
| 2020 Extrusion | | |
| Display panel | | |
| Extruder | | |
| Hot End | | |

## Printed Parts

## References

### Similar projects

* [Vulcanus MAX](https://www.instructables.com/id/Vulcanus-MAX-CoreXY-Aluminum-Frame-RepRap-3D-Print/)
* [Vulcanus V1](https://www.instructables.com/id/Vulcanus-V1-3D-Printer/)