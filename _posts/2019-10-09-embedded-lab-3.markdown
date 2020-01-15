---
layout: post
title: "Embedded Lab #3"
date: "2019-10-09 10:48:42 -0600"
author: Garrett Woodbridge
tags: embedded
---
Another lab finished in Embedded Computer Systems! In this lab, we began interfacing with the Kobuki robot as well as a USB joystick. This involved 2 required exercises. There were also 2 bonus exercises that required implementing a Wi-Fi client and/or server to handle the joystick commands.

## Exercise 1
For this exercise, we were required to move the Kobuki around a predefined course.

![Embedded Lab 3 Track](/assets/img/2019/09/embedded-lab-3-track.png)

To do this, we first opened a serial connection to the Kobuki. Then, we could loop through our movement function a set number of times to navigate the track. The movement function takes in a speed and radius variable that can be used to move the Kobuki in straight lines or curves with a set radius:
{% highlight cpp %}
/*Starting at bottom left corner, travel
along bottom side for 100 cm
speed - 100 mm/s for 10 s*/
for(int i=0; i<500; i++) {
	movement(100, 0);
}

/*Rotate the Kobuki 90 degrees counterclockwise*/
for (int i=0; i<220; i++){
	movement(45,1);
}


/*Move along left side*/
for(int i=0; i<275; i++) {
	movement(100, 0);
}

/*Move along semicircle*/
for(int i=0; i<850; i++) {
	movement(100, 540);
}

/*Move along right side*/
for(int i=0; i<250; i++) {
	movement(100, 0);
}

/*Rotate the Kobuki 90 degrees counterclockwise*/
for (int i=0; i<110; i++){
	movement(45,1);
}

//Close the serial stream to the Kobuki
//Not doing this will result in an unclean disconnect
//resulting in the need to restart the Kobuki
//after every run.
serialClose(kobuki);
return(0);
{% endhighlight %}

## Exercise 2
In our next exercise, we were required to implement a joystick to control the Kobuki. We decided to use the D-Pad up and down for forward and backward. The left and right D-Pad buttons were used to rotate 90 degrees clockwise and counterclockwise. We also used the Start button to stop movement and the Select button to exit the script cleanly.

{% highlight cpp %}
JoystickEvent event;
if(joystick.sample(&event)){
	// if event is button
	if (event.isButton() == 1){
		//printf("Button %u is %s\n", event.number, event.value == 0 ? "up" : "down");

		// if button id = 7 and is pressed stop movement
		if(event.number == 7 && event.value != 0){
			movement(0,0);
		}

		// if button id = 8 and is pressed close connection
		if(event.number == 8 && event.value != 0){
			serialClose(kobuki);
			return(0);
		}
	}
	// D-Pad Check
	if (event.isAxis() == 1){
		if(event.number == 0 && event.value == -32767){
			movement(45,1);
		}
		if(event.number == 0 && event.value == 32767){
			movement(45,-1);
		}
		if(event.number == 1 && event.value == -32767){
			movement(-100, 0);
		}
		if(event.number == 1 && event.value == 32767){
			movement(100,0);
		}
	}
}
{% endhighlight %}

## Bonus Exercise 3
For this third exercise, we had the option to create a client that received commands from a server to control the Kobuki. To do this, we create a socket that begins listening for data. We pass this data into a function that looks for the predefined delimiters to separate the data and assign global variables that we can check in our main function.

{% highlight cpp %}
//Read data from client
readData();

if(int_ValUp == 1){
  movement(-100, 0);
}
if(int_ValDown == 1){
  movement(100,0);
}
if(int_ValLeft == 1){
  movement(45,1);
}
if(int_ValRight == 1){
  movement(45,-1);
}
if(int_ValStart == 1){
  movement(0,0);
}
if(int_ValSelect == 1){
  close(new_socket);
	serialClose(kobuki);
	exit(0);
}
{% endhighlight %}

## Bonus Exercise 4
In this final exercise, we had the option to create a webserver that took joystick commands and sent them to a client running on another Raspberry Pi. To do this, we created a socket that the client could connect to. Then, we used the joystick reading from Exercise 2 and saved the data in separate strings. Finally, we could combine all of the strings with the pre-defined delimiters and send the string to the client.

{% highlight cpp %}
/*Concatenate the values together and send them as
one single object*/
char *joystickInfo;
strcopy(joystickInfo,"U");
strcat(joystickInfo,char_ValUp);
strcopy(joystickInfo,"D");
strcat(joystickInfo,char_ValDown);
strcopy(joystickInfo,"L");
strcat(joystickInfo,char_ValLeft);
strcopy(joystickInfo,"R");
strcat(joystickInfo,char_ValRight);
strcopy(joystickInfo,"S");
strcat(joystickInfo,char_ValStart);
strcopy(joystickInfo,"E");
strcat(joystickInfo,char_ValSelect);

/*Print the data stream to the terminal*/
cout << joystickInfo << endl;

/*Send the data to the server*/
send(sock, joystickInfo, strlen(joystickInfo), 0);
{% endhighlight %}
