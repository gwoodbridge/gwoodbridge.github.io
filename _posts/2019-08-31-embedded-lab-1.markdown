---
layout: post
title: "Embedded Lab #1"
date: "2019-08-30 21:14:57 -0600"
tags: embedded
---
We finally finished our first lab for Embedded Computer Systems (ECEN 4213)! In this lab we covered the basics of a Raspberry Pi and created a Digital-Analog Converter (DAC). This involved 4 separate exercises.

## Exercise 1
For this exercise, we were required to wire up 4 LEDs and make them blink to count in binary (0000, 0001, 0010, etc).

![Raspberry Pi Wiring Diagram](/assets/img/8-30-2019_Embedded-Exercise-1.PNG)

To do this, we first declared the necessary GPIO pins and the binary order within arrays:
```
//Variables
const int GPIO_PINS[4] = {22, 23, 24, 25};
int binary[16][4] = {{0,0,0,0},{0,0,0,1},{0,0,1,0},{0,0,1,1},{0,1,0,0},{0,1,0,1},{0,1,1,0},{0,1,1,1},{1,0,0,0},{1,0,0,1},{1,0,1,0},{1,0,1,1},{1,1,0,0},{1,1,0,1},{1,1,1,0},{1,1,1,1}};
```

To write the status of the LEDs, we chose to write a separate function that takes the 4 digits of binary, loops through them, and writes the individual status to the GPIO pins:
```
//Write leds high or low based on input
void ledWrite(int input[]){
	for(int i = 0; i < 4; i++){
		if(input[i] == 0){
			digitalWrite(GPIO_PINS[i], LOW);
		}
		else{
			digitalWrite(GPIO_PINS[i], HIGH);
		}
	}
}
```

So in our main function, all we had to do was loop through the ledWrite function in 1 second intervals and pass in the correct binary count:
```
//Call led write function and cycle through
for(int j = 0; j < 16; j++){
	ledWrite(binary[j]);
	sleep(1);
}
```

## Exercise 2
In our next exercise, we were required to implement a momentary switch that when pressed, turns on an LED.

![Raspberry Pi Wiring Diagram Exercise 2](/assets/img/8-30-2019_Embedded-Exercise-2.PNG)

Reading a value from a momentary switch can be tricky because the switch value can bounce multiple times before settling in at the correct value. To fix this, you have to implement a debounce check. The main goal of the debounce check is to read the value from the switch a few times with a slight delay between readings. If the readings are all the same, you can assume the switch is pressed. This is not a perfect debounce check but it served the purpose of this lab. Once our debounce check was complete, we simply toggled the status of the LED:
```
//If there is a change in the state of the
//switch, the state of the LED must also change.
if(debounceCheck() == 1)
{
	//Toggle the lED
	digitalWrite(22, !digitalRead(22));
}
```

## Exercise 3
For this third exercise, we used a potentiometer and an external Analog-Digital Converter (ADC) to read in and print the voltage output of the potentiometer.

![Raspberry Pi Wiring Diagram Exercise 3](/assets/img/8-30-2019_Embedded-Exercise-3.PNG)

This exercise was very easy to implement. We communicated to the external ADC via I2C. This returned the digital reading from the ADC. However, we have to convert that to the correct analog voltage to print out in the terminal. Knowing that our maximum voltage is 5V, we can use the following formula:
$$Actual Voltage = Measured Voltage / 2047 * 5V$$

## Exercise 4
In this final exercise, we created an R-2R Ladder DAC and reread the values with the ADC from the previous exercise.

![Raspberry Pi Wiring Diagram Exercise 4](/assets/img/8-30-2019_Embedded-Exercise-4.PNG)

To start, we implemented both the ledWrite function from the first exercise and the ADC read function from the third exercise. Finally, we could cycle through the binary values and read the corresponding analog voltage.

```
//Call pin write function and cycle through
	for(int j = 0; j < 16; j++){
		pinWrite(binary[j]);

		sleep(1);

		//Read value from ADC
		actualV = adcVal();

		//Print to terminal
		cout << "The Analog voltage for the input ";
		for(int k = 0; k < 4; k++){
			cout << binary[j][k];
		}
		cout << " is " << setprecision(4) << actualV << endl;

		//wait for 1 second
		sleep(1);
	}
```
