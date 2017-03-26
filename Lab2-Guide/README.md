# Lab 2 Guide

## Resources

* I2C in Arduino is done with the [Wire Library](https://www.arduino.cc/en/Reference/Wire)
* [Accelerometer Datasheet](https://cdn-shop.adafruit.com/datasheets/LIS3DH.pdf)
* Schematic of breakout:
![Schematic of breakout](sensors_sch.png)
* Arduino pinout
![Arduino Pinout](Arduino-Uno-R3-Pinouts.png)
  
## Steps

1)	Set up hardware for i2c communication  

* i2c Address is 0x18
	* Read the ```WHO_AM_I``` register and print to serial (should get 0x33)
		* Debug until this is right
		* Use scobe probe and observe what is happening on SCL and SDA lines **(take screenshot and include in the lab report)**

2)	Read X,Y,Z accelerometer values in the loop (print to serial)  
**Note:** accelerometer values are 16 bit values.  The registers for the X value are ```OUT_X_L``` and ```OUT_X_H``` which stand for the high and low parts of the number (8 bits each).  You will have to do some bitwise math to combine these two into a 16 bit number, here is pseudocode: 

```
int accel_x = (OUT_X_H << 8) & OUT_X_L.  
```
  
The << is a bitwise shift, so we shift the high half up 8 bits, and then and it with the lower half.
  
**Lab Report:** You'll notice that when the accelerometer is not moving, there is still some acceleration. What is the source of this acceleration? 

3)	Enabling Click on Z axis  

* Enable single click ```CLICK_SRC```
* Enable z click ```CLICK_SRC```
* Enable single click on z-axis ```CLICK_CFG```
* Enable click on INT1 ```CTRL_REG3```
* Set Time Limit ```TIME_LIMIT``` to 10
* Set Time Latency ```TIME_LATENCY``` to 20
* Set Time Window ```TIME_WINDOW``` to 255

Test click thresholds:  
* Set click threshold ```CLICK_THIS``` to 20  
* Test clicks and observe ```INT1``` pin on oscilloscope **(take a screenshot and explain what is happening)** 
* Change threshold until consistent click detection 

**Extra Credit:** Turn on three LEDs based on the readings from the accelerometer.
 
**Lab Report:** 

* Take a picture of the oscilloscope with detection and explain what is happening.
* Copy your console for the report.
* Upload all code for the report as well.
* Upload a picture of the three LEDs turning on for extra credit as well.
* There is a guide for Markdown format. If you have time and can figure it out (it is a scripting language used by GitHub) try writing the lab report in it.
  
**Note:**  
![Latency pic](sensors_click.png)

