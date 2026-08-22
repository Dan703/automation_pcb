# TV Night Shutoff

A custom PCB that automatically turns off a TV (or any IR-controllable device) if it's left on overnight, using ambient light sensing and non-invasive current sensing to fire an IR "power off" command.

Designed from schematic to fabrication-ready Gerbers in KiCad, built around a bare-metal ATmega328P-PU.

## How it works

1. An LDR (photoresistor) voltage divider watches ambient room light. When the room is dark, the system starts polling for a brightness spike.
2. A clamp-style AC current transformer (SCT-013-000) around the TV's power cord confirms the TV is actually drawing power, so the system never fires an IR "power" toggle when the TV is already off (most remotes use a single toggle command for power, so blindly firing it could turn the TV *on*).
3. When both conditions are met, an IR LED (driven through a BJT switching stage) sends the TV's captured NEC power code.
4. A short cooldown period prevents immediately re-triggering.

## Hardware

- **MCU:** ATmega328P-PU (DIP-28), 16MHz crystal, programmed via a 2x3 AVR ISP header
- **Power:** 9V barrel jack input, L7805 linear regulator to 5V
- **Light sensing:** LDR + 10kΩ divider into ADC0
- **Current sensing:** SCT-013-000 clamp, biased divider + burden resistor into ADC1
- **IR transmitter:** TSAL6400 IR LED, 2N2219A PBFREE NPN driver transistor
- **Status LED:** simple GPIO-driven indicator

## Firmware

Written in Arduino-flavored C++, flashed via ISP (no bootloader USB connection on the board itself). Uses the [IRremote](https://github.com/Arduino-IRremote/Arduino-IRremote) library for NEC protocol transmission.


### Programming

1. Flash `ArduinoISP` onto an Arduino Uno.
2. Wire the Uno to the board's ISP header (MOSI/MISO/SCK/RESET/VCC/GND).
3. In Arduino IDE: Board = "Arduino Uno", Programmer = "Arduino as ISP".
4. Burn Bootloader (once per chip).
5. Upload the firmware sketch via "Upload Using Programmer".

## Status

🚧 **In progress.** Board is currently fully soldered and does function as expected! LDR value calibration and current sensor firmware are being currently tested. Videos/demos of final product will be posted soon as well. (see png files for 

## Possible extensions

The same sense → decide → actuate structure generalizes beyond a TV: energy-saving shutoff for any IR-controllable appliance, parental screen-time enforcement, or safety-focused auto-shutoff for higher-risk devices (e.g. space heaters), with straightforward sensor substitution (e.g. a PIR motion sensor instead of the LDR for presence-based automation).
