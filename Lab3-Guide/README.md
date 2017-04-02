#Lab 3 - Clock and Intterupts 

The first part of this lab will explore some simple implementation of intteurpts on the UNO. Normally, we would change some registers and tap ports, but since Lab 2 was heavy on these items, we will use an easier "on the fly" coding method. 

##Intterupt on Digital Pin 3
We will tap into the external interrupt capabilities of the UNO on digital pin 3. Follow these steps:

* **Step 1:** Connect one side of a pushbutton to the digital pin 3 port, and the other side to GND. Don't push the pushbutton yet. You might over power the terminal and blow a pin 

* **Step 2:** Creating an ISR Variable

Make a variable that can be accessed in any scope of the program. This is done by using the `volatile` type cast on your variable of choice. This varibale will help keep state of your program. It should look something like this: 

	volatile int my_var 
	
* **Step 3:** In your `setup()`, make the pinMode of digital pin 3 be an input. Also use `digitalWrite()` to make pin 3 `HIGH`. Doing this enables a pullup resistor to VDD. When you push the pushbutton now, D3 will be grounded, but when unpushed, D3 is high. 

* **Step 4:** Read up on the `attachInterrupt()` and `digitalPinToInterrupt()` functions. Use these to attach a custom ISR function to the digitial pin 3. You will be asked to choose what type of trigger you want the ISR to trigger on, think before you choose. 

 	**Note****: Some platforms of the Arduino IDE do not support the `digitalPinToInterrupt()` fucntion. If you find this to be true for your IDE, the interrupt pin number for pin 3 is 0. So just plug that into the arguements for your interrupt attachment. 
 	
* **Step 5:** In your ISR() function, never print anything to the serial terminal or use delays or even `millis()`. This could create interrupt 'Inception' and make the processor very unhappy. However, in your function, do something to that volatile variable you create earlier. Maybe increase it, decrease it, toggle it. 

* **Setp 6:** Use the `Serial.println()`function to print out the volatile varibale in your `loop()`. Observe the output when the button is pushed. Does your function work?

##Prescaler and Clocks

For this part of the lab, we will change the frequnecy of one our reference clocks to manipulate the frequency of a PWM pin. 

* **Step 1:** Create a new sketch for this code as we dont want to interfere with pervious code that rely on the clocks we are manipulating 
* **Step 2:** Set pins 3 and 11 to be outputs using `pinMode()`
* **Step 3:** Use `analogWrite()` to make a PWM signal on pin 3. Measure the frequency and duty cycle on the oscilliscope.
* **Step 4:** In your `setup()`, write in the following code to set the reigsters of Timer 2 which controls pins 3 and 11 for PWM generation. This code tells the UNO that you want to create a phase correct PWM signal on the pins. 

	` TCCR2A = _BV(COM2A1) | _BV(COM2B1) | _BV(WGM20); `


* **Step 5:** Add in the prescaler with this code:

	`TCCR2B = TCCR2B & 0b11111000 | <setting>`

Where the setting input is choosen from the table below. Your goal is to make the frequency on the pins to be 3.906 kHz. You know the reference clock is 16Mhz. So find the prescaler that would make the new ref clock equal to 3.906 kHz. 

		Setting	    | Presclar Value
		------------- | -------------
		0x01			|1		0x02			|8		0x03			|32		0x04			|64		0x05			|128		0x06			|256		0x07			|1024			 


* **Step 6:** Now set the duty cycle of pin 3 to 13% and the duty cycle of pin 11 to 85% with the following code. The value should be between 0 and 255.  


	`OCR2A = //Your value here, sets pin 3 duty cycle`
	
	`OCR2B = //Your value here, sets pin 11 duty cycle`

Remeber this Timer2 that we are editing is a 8 bit timer and thus has 255 cycle to one second. Thus to get a duty cycle of 50%, Timer2 need to only count 255/2 cycles before switching the output states. 

* **Step 7:** Check the frequnecy and duty cycles on pins 3 and 11 with the oscilliscope. 

 
