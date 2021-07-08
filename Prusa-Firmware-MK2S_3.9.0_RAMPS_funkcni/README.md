# Prusa Firmware 3.9.0 (MK2S) for Arduino RAMPS 1.4 board 



<div align="center">
  <a href="https://www.youtube.com/watch?v=BbjC9gWVAi0"><img src="https://img.youtube.com/vi/BbjC9gWVAi0/0.jpg" alt="IMAGE ALT TEXT"></a>
</div>

This repository contains the source code of a working firmware running on an Arduino RAMPS board for my Prusa i3 Bear style 3D printer (based on an [Original Prusa i3](https://prusa3d.com/)).

The latest official builds can be downloaded from [Prusa Drivers](https://www.prusa3d.com/drivers/). Pre-built development releases are also [available here](https://github.com/prusa3d/Prusa-Firmware/releases).

The firmware for the Original Prusa i3 printers is proudly based on [Marlin 1.0.x](https://github.com/MarlinFirmware/Marlin/) by Scott Lahteine (@thinkyhead) et al. and is distributed under the terms of the [GNU GPL 3 license](LICENSE).

Please read the whole document, especially the ***Important*** section at the bottom.


## 1. Specs

This firmware is configured for a Prusa Bear MK2S fitted with an Arduino RAMPS 1.4 and A4988.

### Power supply
* [Original Prusa 12V 240W PSU](https://shop.prusa3d.com/en/mk2mk2s/176-psu-12v-240w.html)

### Motherboard
* [RAMPS 1.4](https://reprap.org/wiki/RAMPS_1.4)

### Printer frame
* [Prusa i3 Bear Full Upgrade](https://github.com/gregsaun/prusa_i3_bear_upgrade/tree/master/full_upgrade) (MK2S)
* [Prusa i3 Bear Printed parts on Thingiverse](https://www.thingiverse.com/pekcitron/designs) (Grégoire Saunier)
* [Prusa i3 Bear Printed parts on Github](https://github.com/gregsaun/prusa_i3_bear_upgrade/tree/master/full_upgrade) (Grégoire Saunier)

### Stepper motor drivers
* [A4988 (from G3D)](https://reprap.org/wiki/G3D_driver) on X/Y/Z/E axis

### Steppers
* **X-axis:** 1.8° motor
* **Y-axis:** 1.8° motor
* **Z-axis:** 1.8° motor
* **Extruder:** 1.8° motor
* **Microstepping:** 1/16

### Heatbed
* [Original Prusa MK42 12V with ATC Semitec 104GT-2 thermistor](https://shop.prusa3d.com/en/mk2mk2s/119-heatbed-mk42-12v-assembly.html)

### Hotend
 * Direct drive E3D V6 clone
 * 20W 12V Heater Cartridge
 * EPCOS 100k thermistor
 * [PINDA V1](https://shop.prusa3d.com/en/mk2mk2s/127-pinda-probe-v1.html?search_query=pinda&results=2) (Reference: REP-PIN-DA0)

### LCD Controller
* [GADGETS3D G3D LCD/SD Controller](https://reprap.org/wiki/RAMPS_1.3/1.4_GADGETS3D_Shield_with_Panel)
* (optional) [RERAP Discount Smart Controller](https://reprap.org/wiki/RepRapDiscount_Smart_Controller) (can be easily set in Configuration_prusa.h)

### Endstops
* **Mehcanical:** X/Y
* **Inductive (PINDA):** Z

### Wiring Guide
* [Reprap RAMPS 1.4](https://reprap.org/wiki/RAMPS_1.4)
* PINDA wiring:
  | Endstop  | Endstop pin  | PINDA wire |
  | :---:  | :---:  | :---:  |
  | Z-MIN  | VCC (+)  | Brown  |
  | Z-MIN  | GND (-)  | Blue  |
  | Z-MIN  | SIGNAL (S)  | Black  |


## 2. Firmware

The firmware has been modified in many places and files to get it fully working.
All changes are marked with text `/*RAMPS*/`, so it can be easily traced using **Find/Search** option in text editors (or software like Visual Studio or VS Code).

### Features and changes:
* added preheat option to preheat only nozzle (for maintanage, for example)
* added `Init. SD card` to manually initialize SD card if it fails to load when inserted in SD slot
* HW Setup menu changes to `Live Z presets` if a steel sheet is not used, otherwise it shows `Sheets` options
* removed `Mode` menu on LCD (since I use  A4988, mode can't be set to silent, power or auto mode)
* mesh bed leveling has 3x3 and 7x7 options + magnets compensation
* new XYZ calibration
* linear advance 1.5
* Added a factory reset menu `All but statistics` to keep info about hours and filament used
* `First layer calibration` compatible also with 2.85mm filaments 

### Edited files:
* boards.h
* config.h
* configuration.h (check your probe offset settings)
  * `#define X_PROBE_OFFSET_FROM_EXTRUDER -25`
  * `#define Y_PROBE_OFFSET_FROM_EXTRUDER -5 //-29`
  * `#define Z_PROBE_OFFSET_FROM_EXTRUDER -12.35`
* heatbed_pwm.cpp
* Marlin.h
* Marlin_main.cpp
* mesh_bed_calibration.cpp 
(check settings for your setup so that the probe hits all bed points - use Pronterface output to help define the right values)
  ```
  #if MOTHERBOARD == BOARD_RAMPS_14_EFB
    //MK42 BED //if XYZ calibration fails, check serial output in Pronterface and adjust the values accordingly!
    36.5f - BED_PRINT_ZERO_REF_X - X_PROBE_OFFSET_FROM_EXTRUDER - SHEET_PRINT_ZERO_REF_X,	//36,5 - 2 - 23 - 0 = 11,5
    16.1f - BED_PRINT_ZERO_REF_Y - Y_PROBE_OFFSET_FROM_EXTRUDER - SHEET_PRINT_ZERO_REF_Y,	//16,4 - 9,4 - 5 - 0 = 1,7

    239.5f - BED_PRINT_ZERO_REF_X - X_PROBE_OFFSET_FROM_EXTRUDER - SHEET_PRINT_ZERO_REF_X,	//239,5 - 2 - 23 - 0 = 214,5
    16.1f - BED_PRINT_ZERO_REF_Y - Y_PROBE_OFFSET_FROM_EXTRUDER - SHEET_PRINT_ZERO_REF_Y,	//16,4 - 9,4 - 5 - 0 = 1,7

    239.5f - BED_PRINT_ZERO_REF_X - X_PROBE_OFFSET_FROM_EXTRUDER - SHEET_PRINT_ZERO_REF_X, //239,5 - 2 - 23 - 0 = 214,5
    212.4f - BED_PRINT_ZERO_REF_Y - Y_PROBE_OFFSET_FROM_EXTRUDER - SHEET_PRINT_ZERO_REF_Y, //212,4 - 9,4 - 5 - 0 = 198

    36.5f - BED_PRINT_ZERO_REF_X - X_PROBE_OFFSET_FROM_EXTRUDER - SHEET_PRINT_ZERO_REF_X,	//36,5 - 2 - 23 - 0 = 11,5
    212.4f - BED_PRINT_ZERO_REF_Y - Y_PROBE_OFFSET_FROM_EXTRUDER - SHEET_PRINT_ZERO_REF_Y	//212,4 - 9,4 - 5 - 0 = 198
  #else
  ```
* mesh_bed_calibration.h
(check settings for your setup so that the probe hits all bed points - use Pronterface output to help define the right values)
  ```
  #define BED_ZERO_REF_Y (- 0.6f + Y_PROBE_OFFSET_FROM_EXTRUDER + 2.f) // -0.6 + 5 + 2 = 6.4
  #define BED_X0 (13.f - BED_ZERO_REF_X)	//13 - 1 = 12
  #define BED_Y0 (8.4f - BED_ZERO_REF_Y)	//8.4 - 6.4 = 2
  #define BED_Xn (216.f - BED_ZERO_REF_X) //216 - 1 = 215
  #define BED_Yn (204.4f - BED_ZERO_REF_Y)//204.4 - 6.4 = 198
  ```
* pins.h  
* sm4.c  
  * corrected pinout mapping for motors to reflect Ramps schematics
  * added function to re-calculate steps if we use different pulleys
* swi2c.c
* system_timer.c
* temperature.cpp
  * reconfigured timers
  * redefined old PWM for bed heating
* timer02.c:
  * reused old definition of timer02
* timer02.h:
  * reused old definition of timer02
* tmc2130.cpp
* uart2.c
* ultralcd.cpp
  * Edit parameter `0.f` to a higher or lower value if having problem with Z calibration (line 3524)
  ```
  // increase (+) to go lower, decrease (-) to go higher
  current_position[Z_AXIS] = Z_MAX_POS + 0.f;  
  ```
  * added "Init. SD card" menu option to manually initialize SD card if it fails when inserted in SD card slot  
* w25x20cl.c
* w25x20cl.h
* xyzcal.cpp
  * added bed points for MK42 bed (set X and Y coordinates to fit your bed)
  ```
  #if MOTHERBOARD == BOARD_RAMPS_14_EFB
    const int16_t xyzcal_point_xcoords[4] PROGMEM = { 1150, 21450, 21450, 1150 };
    const int16_t xyzcal_point_ycoords[4] PROGMEM = { 600, 600, 19750, 19750 }; 
  #endif //!MOTHERBOARD == BOARD_RAMPS_14_EFB
  ```
### Added new files:
* pins_Ramps.h
* Configuration_prusa.h

### Pronterface 
If having problems with calibration when the probe is searching the calibration points, I recommend to use Pronterface output to find the correct values.


## 3. IMPORTANT
If your setup is different than mine you have to modify parameters and values to fit your needs. 
**Use at your own risk!**

Firmware was successfully compiled and tested with Arduino 1.8.5.
Before compiling, be sure you have modified the file `platform.txt`.
     
* The `platform.txt` file can be found in Arduino instalation directory (`"C:\Program Files (x86)\Arduino\hardware\arduino\avr"`), or after Arduino has been updated at: `"C:\Users\(user)\AppData\Local\Arduino15\packages\arduino\hardware\avr\(version)"`. If you can locate the file in both places, file from user profile is probably used.
       
* Add `"-Wl,-u,vfprintf -lprintf_flt -lm"` to `"compiler.c.elf.flags="` before existing flag `"-Wl,--gc-sections"`  

    For example:  `"compiler.c.elf.flags={compiler.warning_flags} -Os -g -flto -fuse-linker-plugin -Wl,-u,vfprintf -lprintf_flt -lm -Wl,--gc-sections"`

Before making the inital Wizard calibration, it is mandatory to do a **full factory reset to erase all eeprom data!**

1. Press and hold the control knob
2. Power up the printer by turning the PSU switch on
3. Release the control know when you hear a beep

If done correctly, the LCD screen will show a menu with 5 options.
- Language
- Statistics
- Shipping prep
- All data
- All but statistics

Select *All data* and wait for it to complete.
After that you can continue with calibration.
If calibration fails in many retries, you may have to edit firmware parameters in calibration sections.
Whenever a new upload of firmware is done, the factory reset procedure must be repeated!


# Enjoy and happy printing! :)
