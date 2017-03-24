# Lab 2 Lecture Review

## Brief Reminder on i2c:

I2C communication uses 2 lines to transmit and receive data: an SCL line which sends a constant train of square pulses, and an SDA line which data is transmitted on. Master devices initiate communication with slaves by referring to them using their specific address (found in datasheet), and then either write data to the slave or request (read) data from it.

[**Read info**] (https://www.arduino.cc/en/Tutorial/SFRRangerReader)  
Code reproduced here:  

	```
	// I2C SRF10 or SRF08 Devantech Ultrasonic Ranger Finder
	// by Nicholas Zambetti <http://www.zambetti.com>
	// and James Tichenor <http://www.jamestichenor.net>
	
	// Demonstrates use of the Wire library reading data from the
	// Devantech Utrasonic Rangers SFR08 and SFR10
	
	// Created 29 April 2006
	
	// This example code is in the public domain.
	
	
	#include <Wire.h>
	
	void setup() {
	  Wire.begin();                // join i2c bus (address optional for master)
	  Serial.begin(9600);          // start serial communication at 9600bps
	}
	
	int reading = 0;
	
	void loop() {
	  // step 1: instruct sensor to read echoes
	  Wire.beginTransmission(112); // transmit to device #112 (0x70)
	  // the address specified in the datasheet is 224 (0xE0)
	  // but i2c adressing uses the high 7 bits so it's 112
	  Wire.write(byte(0x00));      // sets register pointer to the command register (0x00)
	  Wire.write(byte(0x50));      // command sensor to measure in "inches" (0x50)
	  // use 0x51 for centimeters
	  // use 0x52 for ping microseconds
	  Wire.endTransmission();      // stop transmitting
	
	  // step 2: wait for readings to happen
	  delay(70);                   // datasheet suggests at least 65 milliseconds
	
	  // step 3: instruct sensor to return a particular echo reading
	  Wire.beginTransmission(112); // transmit to device #112
	  Wire.write(byte(0x02));      // sets register pointer to echo #1 register (0x02)
	  Wire.endTransmission();      // stop transmitting
	
	  // step 4: request reading from sensor
	  Wire.requestFrom(112, 2);    // request 2 bytes from slave device #112
	
	  // step 5: receive reading from sensor
	  if (2 <= Wire.available()) { // if two bytes were received
	    reading = Wire.read();  // receive high byte (overwrites previous reading)
	    reading = reading << 8;    // shift high byte to be high 8 bits
	    reading |= Wire.read(); // receive low byte as lower 8 bits
	    Serial.println(reading);   // print the reading
	  }
	
	  delay(250);                  // wait a bit since people have to read the output :)
	}
	
	
	/*
	
	// The following code changes the address of a Devantech Ultrasonic Range Finder (SRF10 or SRF08)
	// usage: changeAddress(0x70, 0xE6);
	
	void changeAddress(byte oldAddress, byte newAddress)
	{
	  Wire.beginTransmission(oldAddress);
	  Wire.write(byte(0x00));
	  Wire.write(byte(0xA0));
	  Wire.endTransmission();
	
	  Wire.beginTransmission(oldAddress);
	  Wire.write(byte(0x00));
	  Wire.write(byte(0xAA));
	  Wire.endTransmission();
	
	  Wire.beginTransmission(oldAddress);
	  Wire.write(byte(0x00));
	  Wire.write(byte(0xA5));
	  Wire.endTransmission();
	
	  Wire.beginTransmission(oldAddress);
	  Wire.write(byte(0x00));
	  Wire.write(newAddress);
	  Wire.endTransmission();
	}
	
	*/

	```

[**Write info**] (https://www.arduino.cc/en/Tutorial/DigitalPotentiometer)  
Code reproduced here:

	```
	// I2C Digital Potentiometer
	// by Nicholas Zambetti <http://www.zambetti.com>
	// and Shawn Bonkowski <http://people.interaction-ivrea.it/s.bonkowski/>
	
	// Demonstrates use of the Wire library
	// Controls AD5171 digital potentiometer via I2C/TWI
	
	// Created 31 March 2006
	
	// This example code is in the public domain.
	
	// This example code is in the public domain.
	
	
	#include <Wire.h>
	
	void setup() {
	  Wire.begin(); // join i2c bus (address optional for master)
	}
	
	byte val = 0;
	
	void loop() {
	  Wire.beginTransmission(44); // transmit to device #44 (0x2c)
	  // device address is specified in datasheet
	  Wire.write(byte(0x00));            // sends instruction byte
	  Wire.write(val);             // sends potentiometer value byte
	  Wire.endTransmission();     // stop transmitting
	
	  val++;        // increment value
	  if (val == 64) { // if reached 64th position (max)
	    val = 0;    // start over from lowest value
	  }
	  delay(500);
	}

	```


## Brief reminder on what registers are:
  
  * Allocated portion of memory that stores a value
  * Registers store data, determine how a device operates, etc.
  * Allocated portion of memory that stores a value
  * Access a register at any time, and use or change the value
  * 3 ports in the arduino (3 registers for these banks of pins):
  	* B: 8-13
  	* C: analog
  	* D: 0-7
  * DDRX:
  	* Responsible for whether a pin is output or input
  		* Example: DDRD = B11111110;  1-7 are out, 0 is in
  			* We have DDRD, DDRB and DDRC
  * PORTX: (ouputs)
  	* PORTD: B10101000; pins 7,5,3 are high
  	* Note: cannot write 7 bit number to 8 bit register
  * PINX: (inputs)
  	* a = PINB; (just gives the values of the pins that are inputs in pin B)  
  *  Some example code:  
	```  
	DDRD = B11111110;  // sets Arduino pins 1 to 7 as outputs, pin 0 as input
	```
	```
	DDRD = DDRD | B11111100;  // this is safer as it sets pins 2 to 7 as outputs without changing the value of pins 0 & 1, which are RX & TX
	```

  * [More info](https://www.arduino.cc/en/Reference/PortManipulation) on ports/registers in arduino

