---
layout: post
title: "Update #1: Jack Sparrow's Compass"
author: "Garrett Woodbridge"
date: "2020-02-08 13:35:07 -0600"
tags: "compass"
---
### A Small Update
After continuously thinking over this idea and doing some research, I've decided to simplify the project slightly. Instead of using a GPS receiver in the compass, I plan to use a Bluetooth module. Then a phone could interact with the compass through a web interface to provide updated desired destinations as well as using the phones GPS. The great thing about this is there is already an Arduino that fits most of my needs. Presenting the Arduino Nano 33 BLE Board:

![Arduino Nano 33 BLE Board](/assets/img/2020/02/arduino nano 33 ble.png)

This board not only has Bluetooth onboard, it also features a 9 axis inertial measurement unit (IMU) which means it has a 3 axis gyroscope, accelerometer, and a magnetometer. The magnetometer will provide the necessary data to determine which direction North is while the Bluetooth will connect to my web interface.

### A Small Worry
You may have noticed I plan to use a continuous rotation servo. This is necessary for the compass to rotate 360 degrees, however, a continuous rotation servo does not natively provide feedback. This means I can't simply specify for it to rotate a set number of degrees. To provide feedback, I'm going to try a Hall Effect sensor and embedding some magnets along the compass face. Luckily, the compass wasn't known for being entirely accurate within the films.
