---
layout: post
title: "Embedded Lab #2"
date: "2019-09-02 15:29:38 -0600"
author: Garrett Woodbridge
tags: embedded
---
Another lab finished in Embedded Computer Systems! In this lab, we covered the basics of using an LCD screen with a Raspberry Pi. This involved 4 separate exercises.

## Exercise 1
For this exercise, we were required to write Hello World to the screen.

To do this, we first declared the positions on the LCD and the character values that we wanted to use:
{% highlight cpp %}
//Position on LCD
///////////////first row/////////////////////////
int pos00[] = {0,0,0,0,0,0,0};  //first row first column
int pos01[] = {0,0,0,0,0,0,1};
int pos02[] = {0,0,0,0,0,1,0};
int pos03[] = {0,0,0,0,0,1,1};
int pos04[] = {0,0,0,0,1,0,0};
int pos05[] = {0,0,0,0,1,0,1};
int pos06[] = {0,0,0,0,1,1,0};
int pos07[] = {0,0,0,0,1,1,1};
int pos08[] = {0,0,0,1,0,0,0};
int pos09[] = {0,0,0,1,0,0,1};
int pos10[] = {0,0,0,1,0,1,0};
int pos11[] = {0,0,0,1,0,1,1};
int pos12[] = {0,0,0,1,1,0,0};
int pos13[] = {0,0,0,1,1,0,1};
int pos14[] = {0,0,0,1,1,1,0};
int pos15[] = {0,0,0,1,1,1,1};

//Character sent to LCD
int zero[]  		= {0,0,1,1,0,0,0,0};  //display character 0
int one[]   		= {0,0,1,1,0,0,0,1};
int two[]   		= {0,0,1,1,0,0,1,0};
int three[]   		= {0,0,1,1,0,0,1,1};
int four[]   		= {0,0,1,1,0,1,0,0};
int five[]   		= {0,0,1,1,0,1,0,1};
int six[]   		= {0,0,1,1,0,1,1,0};
int seven[]   		= {0,0,1,1,0,1,1,1};
int eight[]   		= {0,0,1,1,1,0,0,0};
int nine[]   		= {0,0,1,1,1,0,0,1};
int H[]   		= {0,1,0,0,1,0,0,0};
int e[]   		= {0,1,1,0,0,1,0,1};
int l[]   		= {0,1,1,0,1,1,0,0};
int o[]   		= {0,1,1,0,1,1,1,1};
int W[]   		= {0,1,0,1,0,1,1,1};
int r[]   		= {0,1,1,1,0,0,1,0};
int d[]   		= {0,1,1,0,0,1,0,0};
int exclamation[]	= {0,0,1,0,0,0,0,1};
int colon[]   		= {0,0,1,1,1,0,1,0};
int space[]   		= {0,0,1,0,0,0,0,0};
{% endhighlight %}

To write to the screen, we had to turn the display on, clear the screen, set the starting position we wanted to write to, and then send what we wanted to write one character at a time:
{% highlight cpp %}
displayOn(); // Turn display on
clearScreen(); // Clear screen

// Set starting address and send characters
setAddress(pos00);
writeChar(H);
writeChar(e);
writeChar(l);
writeChar(l);
writeChar(o);
writeChar(space);
writeChar(W);
writeChar(o);
writeChar(r);
writeChar(l);
writeChar(d);
writeChar(exclamation);
writeChar(space);
{% endhighlight %}

## Exercise 2
In our next exercise, we were required to implement a momentary switch that when pressed, prints its status to the LCD screen.

We used the debounce check from the previous lab to get the status of the switch. Then it was just a simple if statement to print the status to the LCD:
{% highlight cpp %}
if(debounceCheck() == 1){
	//Write Pressed
	clearScreen();
	setAddress(pos00);
	writeChar(P);
	writeChar(r);
	writeChar(e);
	writeChar(s);
	writeChar(s);
	writeChar(e);
	writeChar(d);
	writeChar(space);
}
else{
	// Write Not Pressed
	setAddress(pos00);
	writeChar(N);
	writeChar(o);
	writeChar(t);
	writeChar(space);
	writeChar(P);
	writeChar(r);
	writeChar(e);
	writeChar(s);
	writeChar(s);
	writeChar(e);
	writeChar(d);
	writeChar(space);
}
{% endhighlight %}

## Exercise 3
For this third exercise, we had to get the same result as Exercise 2, but with interrupts instead. Exercise 2 constantly polls the status of the switch which can be taxing on resources. What we can do instead is just look for a change in status, then change the LCD.

{% highlight cpp %}
// The interrupt
wiringPiISR (BUTTON, INT_EDGE_RISING, &risingInterrupt);
{% endhighlight %}

{% highlight cpp %}
// The print function
void risingInterrupt(){
	while(true){
		if(debounceCheck() == 1){
			//Write Pressed
			clearScreen();
			setAddress(pos00);
			writeChar(P);
			writeChar(r);
			writeChar(e);
			writeChar(s);
			writeChar(s);
			writeChar(e);
			writeChar(d);
			writeChar(space);
			delay(100);
		}
		else{
			setAddress(pos00);
			writeChar(N);
			writeChar(o);
			writeChar(t);
			writeChar(space);
			writeChar(P);
			writeChar(r);
			writeChar(e);
			writeChar(s);
			writeChar(s);
			writeChar(e);
			writeChar(d);
			writeChar(space);
			break;
		}
	}
}
{% endhighlight %}

## Exercise 4
In this final exercise, we created a stopwatch using code from the previous sections. The goal is to start a stopwatch with both minute and seconds on the LCD display when the button is pressed. Every additional time the button is pressed, it should print the time to the terminal. If the button is held down for 4 seconds, it should stop the stopwatch and clear the screen.

To start, we implemented the interrupt from the previous exercise. Once within the interrupt, we check to see if the stopwatch has started. If it hasn't, we start the stopwatch. If it has, we need to print the time to the terminal and see how long the button is held down. If it's longer than 4 seconds, reset.

{% highlight cpp %}
void risingInterrupt(){
	if(debounceCheck() == 1){
		//Check if stopwatch is started
		if(started == false){
			//Start Stopwatch
			started = true;
			t1 = high_resolution_clock::now();
		}
		else{
			//Stopwatch already started. Print timeouts to terminal. Poll for reset.

			//Print timeout on button release if less than 4 seconds
			//If held for 4 seconds, set started to false, reset iteration counter.
			time_t t1 = time(0);
			for(int i=0; i<10; i++){
				if(debounceCheck()==1){
					time_t t2 = time(0);
					double time_span = (t2-t1);
					if (time_span > 4){
						started = false;
						iterations = 0;
						break;
					}
				}
				else{
					break;
				}
				delay(700);
			}
			//add and print iteration
			if(started)
			{
				iterations++;
				cout << iterations << get_ordinal(ordinals, iterations) << " Place: ";
				printf(" %02i:%02i\n", minute, second);
			}
		}
	}
}
{% endhighlight %}

You'll notice that we also had to print the correct ordinals to the terminal as well. For those who don't know, ordinals are the letters that come after the numbers when you count. For example, the 'st' in 1st, the 'nd' in 2nd. There are a total of 4 different options. To check, we used the following code:

{% highlight cpp %}
string get_ordinal(string ordinals[], int value){
	value %= 100;

	if(3<value && value<21)
		return ordinals[3];

	switch (value % 10){
		case 1 : return ordinals[0];
				 break;
		case 2 : return ordinals[1];
				 break;
		case 3 : return ordinals[2];
				 break;
		default : return ordinals[3];
				 break;
	}
}
{% endhighlight %}

Finally, we had to handle the minute and second separation on the LCD. Once the seconds go past 60, it should switch over to 1 minute and 0 seconds. Here is how we accomplished this:

{% highlight cpp %}
if(started){
	//Print stopwatch
	high_resolution_clock::time_point t2 = high_resolution_clock::now();
	duration<double> time_span = duration_cast<duration<double>>(t2-t1);
	int duration2 = time_span.count();
	setAddress(pos00);
	if (duration2 / 60 >= 1) {
	  // handle minute
	  minute = duration2 / 60;
	}
	//handle second
	second = duration2 % 60;


	writeChar(numbers[(int)minute/10]);
	writeChar(numbers[minute%10]);

	writeChar(numbers[10]);

	writeChar(numbers[(int)second/10]);

	writeChar(numbers[second%10]);
}
else{
	//clear screen
	clearScreen();
}
{% endhighlight %}
