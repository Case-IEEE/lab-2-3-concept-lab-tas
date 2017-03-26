# Lab 3 Guide
### ISR and Clocks Injection
  
## Steps

For the first part, we will enable some external interrupts on the UNO. This coding method is a wrapper around some of the bit logic code that we would normally use.  Sometimes you will want to use this method when coding gets a little crazy or you are pressed for time. Normally, we edit the registers and set bit masks, but in the interest of time and the fact that you have a lot of other ports/registers to set, we will simplify this a bit. 

**Part 1:**  

* Connect a pushbutton in series with a 10kohm resistor to the power supply and then connect the end to the digital pin #2. 
```

```
External_pin_ISR()
``` 

  

**PWMing the LED for 10s:**   


```
TCCR2A = _BV(COM2A1) | _BV(COM2B1) | _BV(WGM20);  
```

* ```_BV()``` stands for bit value and converts the input to its bit value. Each input is mapped to certain conditions in the UNO. COM2A1 and COM2B1 set the PWM to be non inverted waveforms. WGM20 and WGM20 sets the item to phase correct pwm which is a better waveform result. Consult the data sheet for the ATMEGA chip on your UNO to see what other options there are. 

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

```