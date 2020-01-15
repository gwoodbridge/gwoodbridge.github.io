---
layout: post
title: "Embedded Lab #4"
date: "2019-11-01 19:08:03 -0600"
author: Garrett Woodbridge
tags: embedded
---
Another lab finished in Embedded Computer Systems! In this lab, we covered how to read the sensor values from the Kobuki as well as how to interface with an ultrasonic sensor. This involved 4 separate exercises.

## Exercise 1
For this exercise, we were required to read from the 3 bumper sensor, the 2 wheel drop sensors, and the 3 cliff sensors and print their status to the terminal.

To do this, we referenced the data sheet to determine how to data packet was formatted. Once we knew we were reading the sensor data packet, we could parse through to where the data was for the 3 sensors we needed to read. This data was in hex, so we performed an and operation on the data rather than comparing every possible value:
{% highlight cpp %}
//If the bytes are a 1 followed by 15, then we are
//parsing the basic sensor data packet
if(read == 1){
	if(serialGetchar(kobuki) == 15){
		//Read past the timestamp
		serialGetchar(kobuki);
		serialGetchar(kobuki);

		/*Read the bytes containing the bumper, wheel drop,
		  and cliff sensors.*/
		/*Convert them into a usable data type.*/
		char bumper = serialGetchar(kobuki);
		char wheeldrop = serialGetchar(kobuki);
		char cliff = serialGetchar(kobuki);

		/*Print the data to the screen in the given format.*/
		if ((int(bumper) & 1) == 1){
			cout << "Right Bumper: Pressed" << endl;
		}
		else {
			cout << "Right Bumper: Not Pressed" << endl;
		}

		if ((int(bumper) & 2) == 2){
			cout << "Center Bumper: Pressed" << endl;
		}
		else {
			cout << "Center Bumper: Not Pressed" << endl;
		}

		if ((int(bumper) & 4) == 4){
			cout << "Left Bumper: Pressed" << endl;
		}
		else {
			cout << "Left Bumper: Not Pressed" << endl;
		}

	if ((int(cliff) & 1) == 1){
			cout << "Right Cliff: Triggered" << endl;
		}
		else {
			cout << "Right Cliff: Not Triggered" << endl;
		}

		if ((int(cliff) & 2) == 2){
			cout << "Center Cliff: Triggered" << endl;
		}
		else {
			cout << "Center Cliff: Not Triggered" << endl;
		}

		if ((int(cliff) & 4) == 4){
			cout << "Left Cliff: Triggered" << endl;
		}
		else {
			cout << "Left Cliff: Not Triggered" << endl;
		}
		if ((int(wheeldrop) & 1) == 1){
			cout << "Right Wheel: Dropped" << endl;
		}
		else {
			cout << "Right Wheel: Not Dropped" << endl;
		}

		if ((int(wheeldrop) & 2) == 2){
			cout << "Left Wheel: Dropped" << endl;
		}
		else {
			cout << "Left Wheel: Not Dropped" << endl;
		}
		cout << endl;

		/*Read through the bytes between the cliff sensors and
		the button sensors.*/
		serialGetchar(kobuki);
		serialGetchar(kobuki);
		serialGetchar(kobuki);
		serialGetchar(kobuki);
		serialGetchar(kobuki);
		serialGetchar(kobuki);
		/*Read the byte containing the button data.*/
		char button = serialGetchar(kobuki);

		/*Close the script and the connection to the Kobuki when
		Button 1 on the Kobuki is pressed.*/
		if (int(button) == 2) {
		serialClose(kobuki);
		return(0);
		}
		/*Pause the script so the data read receive rate is
		the same as the Kobuki send rate.*/
		usleep(20000);
	}
}
{% endhighlight %}

## Exercise 2
In our next exercise, we were required to implement an ultrasonic sensor and print the distance measured in centimeters to the terminal.

An ultrasonic sensor works by sending a sound signal and measuring how long it takes to hear it again. Sometimes, it doesn't successfully receive the signal which can throw off your measurements. We fixed this by resending the signal after a specified wait:
{% highlight cpp %}
/*Generate a LOW-HIGH-LOW signal to trigger the sensor*/
/*Use a 2 us delay and then a 5 us delay to slow the script*/
/*speed to a rate at which the ultrasonic sensor can process*/
pinMode(1, OUTPUT);
digitalWrite(1, LOW);
usleep(2);
digitalWrite(1, HIGH);
usleep(5);
digitalWrite(1, LOW);


/*Switch signal to input to read when the signal comes back to sensor*/
/*Calculate delay for t_holdoff and t_min*/
pinMode(1, INPUT);
usleep(865);
high_resolution_clock::time_point t1 = high_resolution_clock::now();
duration<double> time_span;
while (digitalRead(1)==1){
	high_resolution_clock::time_point t2 = high_resolution_clock::now();
	time_span = duration_cast<duration<double>>(t2-t1);
	waittime = time_span.count();
	if(waittime >= .0185){
		break;
	}		
}

if(waittime >= 0.0185){
	continue;
}

//Calculate the distance. Speed of sound is 340m/s
double distance = ((170 * waittime) * 100);
//Print the data.
cout << distance << " cm" << endl;
usleep(1);
{% endhighlight %}

## Exercise 3
For this third exercise, we had to move the Kobuki around a table without it falling or getting stuck in corners. To do this, we set our default movement to be straight forward. If it gets triggered by a flag from the bumper, wheeldrop, or cliff sensors, it backs straight up for 6 seconds before turning for 1 second.

{% highlight cpp %}
/*Read the initial data. If there are no flags,
the default condition is forward.*/
if ((int(bumper) == 0) && (int(cliff) == 0)) {
	movement(70,0);
	reset = 1;
}
else {
	if(reset) {
		for(int i=0; i<30; i++) {
			movement(-50,0);
		}
		reset = 0;
	}
	for(int j=0; j<5; j++) {
		movement(50,1);
	}
}
{% endhighlight %}

## Exercise 4
In this final exercise, we had to navigate the Kobuki through a walled path with bonus points if you don't hit any walls. The path was predefined so we knew what direction the turns needed to be. We started by travelling forward until the ultrasonic sensor read that we were within 20cm of the wall. We then turned 90 degrees the predefined direction. Rinse and repeat until you finish the track. This only works because the path was only made up of 90 degree turns.

{% highlight cpp %}
if(distance < 20) {
	check++;
	if(check%2 == 0) {
		if(rotate == 0) {
			movement(0,0);
			// rotate clockwise 90 degrees
			for (int i=0; i<175; i++){
				movement(70,-1);
			}
			rotate++;
		}
		else {
			movement(0,0);
			// rotate counter clockwise 90 degrees
			for (int i=0; i<200; i++){
				movement(70,1);
			}
		}
	}
}
else {
	movement(100,-25000);
}
{% endhighlight %}
