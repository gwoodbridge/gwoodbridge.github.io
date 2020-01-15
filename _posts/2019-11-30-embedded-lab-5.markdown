---
layout: post
title: "Embedded Lab #5"
date: "2019-11-30 21:15:37 -0600"
author: Garrett Woodbridge
tags: embedded
---
We finished the final lab in Embedded Computer Systems! In this lab, we used a camera and OpenCV to identify and track a colored ball. This involved 2 separate exercises.

## Exercise 1
For this exercise, we were required to interface with the camera and track the x, y, and z position of the ball.

To do this, we defined our Hue, Saturation, and Value ranges that identified the color of the ball. After that, we could take our camera feed frame by frame and use OpenCV's built in functions to modify the image and determine the x and y position of the ball. However, the only thing that changes when the ball is farther away (the z axis) is the balls size in the camera frame. So, we took area measurements every 15cm and determined an exponential function that best fit those data points:
{% highlight cpp %}
Mat imgOriginal;   //This is the matrix that holds the image captured.


bool bSuccess = cap.read(imgOriginal); // read a new frame from camera

if (!bSuccess) //if not success, break loop
{
	cout << "Cannot read a frame from video stream" << endl;
	break;
}

Mat imgHSV;   //create another matrix to hold the HSV image

//Convert the captured frame from BGR to HSV
/*Use the cvtColor function*/
cvtColor(imgOriginal, imgHSV, COLOR_BGR2HSV);

Mat imgThresholded;  //create another matrix to hold the thresholded image

//Threshold the image
/*Use the inRange function*/
inRange(imgHSV, Scalar(iLowH, iLowS, iLowV), Scalar(iHighH, iHighS, iHighV), imgThresholded);

//morphological opening (removes small objects from the foreground)
/*Use the erode function*/
/*Use the dilate function*/
erode(imgThresholded, imgThresholded, getStructuringElement(MORPH_ELLIPSE, Size(5,5)));
dilate(imgThresholded, imgThresholded, getStructuringElement(MORPH_ELLIPSE, Size(5,5)));

//morphological closing (removes small holes from the foreground)
/*Use the dilate function*/
/*Use the erode function*/
dilate(imgThresholded, imgThresholded, getStructuringElement(MORPH_ELLIPSE, Size(5,5)));
erode(imgThresholded, imgThresholded, getStructuringElement(MORPH_ELLIPSE, Size(5,5)));


imshow("Thresholded Image", imgThresholded); //show the thresholded image
imshow("Original", imgOriginal); //show the original image
{% endhighlight %}

{% highlight cpp %}
Moments oMoments = moments(imgThresholded);

double dM01 = oMoments.m01;
double dM10 = oMoments.m10;
double dArea = oMoments.m00;

/*Calculate the position of the ball.*/
double x = (dArea/1000);
double distance = (118-(.581*x)+(.00133*x*x)-(.000000931*x*x*x)); //some formula with dArea;
{% endhighlight %}

## Exercise 2
The second part of this lab required us to use the information gather in the first exercise and have the Kobuki follow the ball while staying 1 meter away. To do this, we implemented a PID control loop that used the Z measurement to maintain the 1 meter distance. To keep the ball centered, we calculated the x error and changed the rotation value in our movement function to compensate.
