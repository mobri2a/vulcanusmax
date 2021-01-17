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