# Pixo-Thinclient

A minimalist firmware for the Pixo Pixel that will expose an HTTP API


## What's the purpose?

The goal for this firmware is to expose a RESTful API, available on http(s). This API will perform functions such as 
* Saving, Loading, and Deleting images from internal storage and RAM
* Displaying chosen image (or animation)
* Reading the state of the accelerometer
* Adding, Reading and Removing configuration options (wifi, brightness, toggles, etc)
* Ability to save TLS/SSL certificate+key to internal storage
* Signed OTA Updating
* More to come!


## What is pixo-pixel?

Pixo Pixel is a 16x16 REB LED display, using an ESP32 microcontroller and a 3-axis Accelerometer + Gyro.

Originally created by [Sean Hoffman](https://github.com/IdleHandsProject/pixopixel) as a Make 100, the hardware is interesting but no general purpose firmware has been created publically. I've contributed to the project a bit in the past [here](https://github.com/alopexc0de/pixopixel/tree/fw_cleanup), in the form of updating the readme and also some expansion/rewriting of example display functions.


## Flashing Firmware Instructions
1. You need to have the current version of the [Arduino IDE](https://arduino.cc) installed
2. Ensure that you have the [Arduino ESP32 Boards](https://github.com/espressif/arduino-esp32) installed 
3. Various other libraries are required for the example firmware; they are listed below under "Libraries used in Example Firmware"
4. Connect your pixo pixel to your computer and start Arduino
5. Under the "Tools" menu, select "Board->Adafruit ESP32 Feather"; Leave Flash frequency and Upload speed the default
6. Under Port, select the device your pixo is communicating over
7. Compile and upload
8. When the Arduino console says "Connecting....", press and hold both the boot and reset buttons simultaniously for 1 second; then release both buttons
9. You should start to see output about flashing
10. When the flash is complete, press the reset button on the pixo to start your new firmware


### Required Libraries
Install these to your sketchbook's `libraries` folder, usually located in `YOUR HOME/Documents/Arduino`

* [Adafruit GFX](https://github.com/adafruit/Adafruit-GFX-Library)
* [Adafruit DotStar](https://github.com/adafruit/Adafruit_DotStar)
* [Adafruit DotStar Matrix](https://github.com/adafruit/Adafruit_DotStarMatrix)
* [ArduinoJson](https://github.com/bblanchon/ArduinoJson)
* [i2cdevlib](https://github.com/jrowberg/i2cdevlib)

**Note** Arduino IDE was complaining about i2cdevlib being invalid as the installation instructions are a bit unclear.
It seems that the ESP32 is not officially supported for use with these libraries, and I had to apply a patch ([#367](https://github.com/jrowberg/i2cdevlib/pull/367)) to get no compile errors.

#### Patching I2Cdev and MPU6050 Libraries
1. Download i2cdevlib and extract the zip file
2. Inside the i2cdevlib/Arduino folder, copy `I2Cdev` and `MPU6050` folders to your `libraries` folder
3. Edit `libraries/I2Cdev/I2Cdev.cpp`, and right under `#include "I2Cdev.h"`, add the following
```
#ifdef ARDUINO_ARCH_ESP32
#define BUFFER_LENGTH I2C_BUFFER_LENGTH
#endif
```
4. Edit `libraries/MPU6050/MPU6050_6Axis_MotionApps20.h` and add the following right above the `DEBUG` section
```
#ifndef ARDUINO_ARCH_ESP32
    typedef void prog_void;
    typedef char prog_char;
    typedef unsigned char prog_uchar;
    typedef int8_t prog_int8_t;
    typedef uint8_t prog_uint8_t;
    typedef int16_t prog_int16_t;
    typedef uint16_t prog_uint16_t;
    typedef int32_t prog_int32_t;
    typedef uint32_t prog_uint32_t;
#endif
```
5. Restart Arduino

There's a whole bunch of other commits in here, but this seems mostly to be a mix of: feature adding, bug fixing, and various other changes that may or may not be related. Feel free to experiment with these at your own discretion.



