# **Finding Lane Lines on the Road**
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image0]: ./test_images_output/solidWhiteCurve.jpg "solidWhiteCurve"


## Overview
---
> When we drive, we use our eyes to decide where to go. The lines on the road that show us where the lanes are act as our constant reference for where to steer the vehicle. Naturally, one of the first things we would like to do in developing a self-driving car is to automatically detect lane lines using an algorithm.

This is the first project of term 1 of the self-driving car nanondegree at [Udacity](https://www.udacity.com/course/self-driving-car-engineer-nanodegree--nd013)

## Reflection
---

### 1. Image Processing Pipeline

My pipeline consists of 5 steps: 
1. Grayscale
1. Gaussian blur to reduce noise
1. Canny edge detection
1. Selection of a ROI (trapeze relative to shape of the iamge)
1. Hough lines including combining the lines for one lane to a single lane

I did not modify the ```draw_lines()``` function. Instead I added an additional function ```select_lanes()``` which operates on the lines found by the ```cv2.HoughLinesP()```.
The function ```select_lanes()``` does the following things: For every line it calculates the slope and extrapolates it to a lower and upper border (arguments y_low and y_up). This is performed inside the function ```calc_intersections()```.
The resulting lines are then classified based on their slope into lines belonging to the left or right lane or outside the lane. Lines which were bundeled to one lane side are then averaged to get a single lane line.
For robustness the function checks if at least one line was found for each lane side. If not, it will return a dummy line (0, 0, 0, 0) for that lane side. ```select_lanes()``` will eventually return a list of two lines for both line sides which is used as input to ```draw_lines()```. 

If you'd like to include images to show how the pipeline works, here is how to include an image: 
In the following an example of the pipeline steps applied to the first test image. Note that the colour threshold is included only for completness although the threshold was set to 0 (as in the pipeline). Hence, the colour threshold has no effect.
![alt text][image0]


### 2. Shortcomings with current pipeline

- The parameters have to be tuned to a specific position of the camera. E.g. the region of interested is very sensible to rotation of the camera.
- When only few strips are visible the slope of the lane is identified in an incorrect way.
- The last video demonstrates that the pipeline cannot handle obscured parts of the image. E.g. in the last video is a part of the front hood of the car visible. Thus the intersections are calculated wrong.

### 3. Possible improvements

- Major improvements can still be done for the extrpolation of the lines of one lane. For example when the lines are averaged each line could be weighted by its length or the difference of its slope to the average slope. Falsely detected lines are usually short and thus would influence the average only little.

- The parameters should be tunable for every video without changing the pipeline
