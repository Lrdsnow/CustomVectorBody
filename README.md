# CustomVectorBody
vector bodyboard, but in the worst way

# Hardware
Heres the fun part, you need an mcu (or really any small device capable of uart & spi, NOT an ESP32 unless you want to multiplex everything), motor drivers that can drive 4x motors, and enough pins to handle the backpack fpc and encoders and the ToF sensor and whatnot, and if you can somehow pull it off, find some cliff sensors
then you just want to wire it up, i used:
- [Waveshare RP2350-Tiny](https://www.waveshare.com/rp2350-tiny.htm) - main mcu, although id probably use something else now like the [Core2350B](https://www.waveshare.com/core2350b.htm)
- [DRV8833 Dev Boards](https://www.aliexpress.com/p/tesla-landing/index.html?productId=3256808863783815) - x2, motor drivers
- [Random 5V Boost Converter](https://www.adafruit.com/product/4654) - power regulator
- [TP4056/TP4057 3.7v Battery Charger](https://www.aliexpress.us/item/3256808124186675.html) - charges the battery
- Any random perfboard will work, or whatever you want to put all of this onto, or just shove it in the body but that just seems messy
- You'll probably also want a 0.5mm 12P FPC Breakout board for the Backpack

then just wire it up, GND to all GND pins, 5v to VBUS's and VDD's including the head's 5v, TP4056/TP4057 out to 5v boost in, all data stuff to gpio like spi, i2c, all the backpack wires, and the head uarts (remember these), motor driver outs to motor wires, etc.

Backpack pinout is also pretty simple its just:
1. LED_DAT
2. LED_CLK
3. Mic pin
4. Mic pin
5. Mic pin
6. 5v or 3.3v
7. Button
8. GND
9. 3.3v (do NOT use 5v)
10. GND
11. not connected
12. touch sensor

# Software
- you are going to want to study this: https://github.com/os-vector/wire-os-victor/tree/main/robot/hal \
basically you kinda just grab your two head UART pins, and start a connection at 3000000 baud,
and just send it something like this:
DATA_FRAME (0x6466) - Send Every 20ms
```
Header (8 bytes):
  - sync: 0x423248aa
  - type: 0x6466
  - size: sizeof(BodyToHead)

Payload (BodyToHead):
  - framecounter (increments each frame)
  - flags: 0x01 (sensors valid)
  - motor positions/deltas (4 motors)
  - cliff sensors (4 values ~1500)
  - battery (voltage, temp, charger status)
  - proximity sensor data
  - touch sensors (button state here!)
  - microphone status
  - audio samples (320 int16 samples)

Footer (4 bytes):
  - CRC32 checksum
```
VERSION (0x7276) - On Request
```
Header + Payload:
  - hw_revision: 5
  - hw_model: 1
  - ein: 16 bytes (serial number)
  - app_version: 16 bytes (version string)
Footer: CRC32
```
ACK (0x6b61) - On Request
```
Header + Payload:
  - status: 3 (ACK_APPLICATION) or -1/-7 for errors
Footer: CRC32
```
