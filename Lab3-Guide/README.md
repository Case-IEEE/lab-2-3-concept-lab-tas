# Lab 3 Guide
### ISR and Clocks Injection
  
## Steps

For the first part, we will enable some external interrupts on the UNO. This coding method is a wrapper around some of the bit logic code that we would normally use.  Sometimes you will want to use this method when coding gets a little crazy or you are pressed for time. Normally, we edit the registers and set bit masks, but in the interest of time and the fact that you have a lot of other ports/registers to set, we will simplify this a bit. 

**Part 1:**  

* Connect a pushbutton in series with a 10kohm resistor to the power supply and then connect the end to the digital pin #2. * Make the digital pin assignment with your other global variables at the top of your code: 
```const byte ext_int_pin = 2;  //Note this is the digital pin on the UN0```* Create a function after your main ```loop()``` block called:

```
External_pin_ISR(){	//Code goes here  } 
``` 
* Please either print a statement to the serial port or maybe even flash an LED if you would like. 
  * Next, we can easily attach an external interrupt with the following function in the ```setup()``` block: [Read up](https://www.arduino.cc/en/Reference/attachInterrupt) on this function: ```attatchinterrupt()``` function* Use this ```attachinterrupt()``` function to incorporate your custom ISR callback function we just made to calibrate the accelerometer to digital pin 2. You will have to set the interrupt mode in the function to RISING or FALLING. Please choose which you would like. REMEMBER! The button is connected to a 5V supply into the digital pin. Thus when the button is pressed, what waveform signature do you want your interrupt to be called on? Be careful, you could make the MCU very unhappy. 

**PWMing the LED for 10s:**   * Lets play with the clock prescaling a bit. We have used the ```analogWrite(pin,value)``` to write PWM signal to an output, but maybe I want to change up the frequency at which I am PWMing. Please write this code in your ```setup()``` function. 
* Step1: Somewhere in your code, use the ```analogWrite()``` function to generate a PWM signal on analog pin 3. Use the oscilloscope to determine the frequency of this waveform. What is the frequency? * Step 2: Now that we know the frequency, we want to change the frequency to something else. Lets do this by manipulating the registers for Timer 2 which is used to create the PWM signal on pins 3 and 11 on the UNO. * Set pins 3 and 11 to output mode using ```pinMode()```* Now we will mess with the timer 2 registers. Each timer register has two ports, A and B. Waveform generation information is split between the two ports.  Use the code below:

```
TCCR2A = _BV(COM2A1) | _BV(COM2B1) | _BV(WGM20);  
```

* ```_BV()``` stands for bit value and converts the input to its bit value. Each input is mapped to certain conditions in the UNO. COM2A1 and COM2B1 set the PWM to be non inverted waveforms. WGM20 and WGM20 sets the item to phase correct pwm which is a better waveform result. Consult the data sheet for the ATMEGA chip on your UNO to see what other options there are. * Now lets set the prescaler and duty cycle:

```TCCR2B = TCCR2B & 0b11111000 | setting```  

* Knowing that timer 2 is an 8 bit timer, the ref clock is 16MHz chose from the available prescaler inputs below to create a frequency of 3.906 kHz 

|    SETTING      | DIVISOR  |
|:------------:|-------------|
|   0x01    |  1  |
|   0x02    |  8 |
|   0x03    |  32 |
|   0x04    |  64  |
|   0x05    |  128 |
|   0x06    |  256|
|   0x07    |  1024 |
* Then lets set the duty cycle of the pins. This is set in the output compare register. Since we have an 8 bit timer, the value range is 0 to 255. This is also the range of the output compare register. The register simply compares the set value to the amount of clock cycles counted. Once the register value is equals the clock count, the output goes low and we restart counting. The next cycle will go high. Please calculate what value is needed for a 13% duty cycle on pin 3 and an 85% duty cycle on pin 11. Measure these on the oscilloscope and verify that the duty cycle and frequency is what we have set. ```OCR2A = //Your value here, sets pin 3 duty cycleOCR2B = //Your value here, sets pin 11 duty cycle
```
