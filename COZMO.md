Custom Vector Body, but its for Cozmo 1.5 (the backpack is a bit different with 1.0)

# Hardware
Heres the fun part, you need an mcu (or really any small device capable of uart & spi, NOT an ESP32 unless you want to multiplex everything), motor drivers that can drive 4x motors, and enough pins to handle the backpack connector and encoders and whatnot, i used:

- [Waveshare RP2350-Tiny](https://www.waveshare.com/rp2350-tiny.htm) - main mcu, although id probably use something else now like the [Core2350B](https://www.waveshare.com/core2350b.htm)
- [DRV8833 Dev Boards](https://www.aliexpress.com/p/tesla-landing/index.html?productId=3256808863783815) - x2, motor drivers
- [Random 5V Boost Converter](https://www.adafruit.com/product/4654) - power regulator
- [TP4056/TP4057 3.7v Battery Charger](https://www.aliexpress.us/item/3256808124186675.html) - charges the battery
- Any random perfboard will work, or whatever you want to put all of this onto, or just shove it in the body but that just seems messy

then just wire it up, GND to all GND pins, 5v to VBUS's and VDD's, TP4056/TP4057 out to 5v boost in, all data stuff to gpio like spi, i2c, all the backpack wires, motor driver outs to motor wires, etc.

Head pinout is also a bit different with Cozmo its:
- 3.3v (i think)
- TRX (UART)
- GND

Backpack is a bit different with cozmo, its combined with one of the motors, id recommend just splitting the JST into multiple connectors
The pinout is very odd for the cozmo backpack, its:
1. LED Power
2. LED Color
3. Button/LED
4. GND

# Software
I have yet to work with the cozmo head, but this is where you should start looking:
https://github.com/kercre123/victor/blob/6246f5a7f2e35e7c78d1f153ce65158967a0a4cc/robot/hal/spine.cpp
