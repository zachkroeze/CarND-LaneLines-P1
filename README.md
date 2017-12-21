# CarND-LaneLines-P1
The first project of the Udacity Self-Driving Car Engineer Nanodegree Program. The project involves using computer vision techniques to find lane markings on the road. This document gives an overview of the lane detection pipeline used to annotate both images and videos.

### Lane Finding Pipeline
The proposed lane finding pipeline consists of several steps which we describe here:

####### 1) Convert image to grayscale
We begin the pipeline by converting the image to grayscale. We do this since colour alone does not give us a clear indication of a lane or non-lane pixel. By converting to grayscale, we can focus on intensity which is a better predictor.

####### 2) Apply Gaussian smoothing
We apply Gaussian smoothing since images are inherently noisy. We want to smooth out this noise so we can better detect gradients in the next step.

####### 3) Apply Canny edge detection
Canny edge detection is a classic computer vision technique which uses the gradient of the image to detect edges. In our situation, it is useful for detection large gradients across the image (as when transitioning from a road pixel to a lane pixel)

####### 5) Search only on a region of interest
This step relies on the fact that the camera position on the vehicle remains constant, and therefore the region of the road in which we would like to search for lane markings is relatively fixed. We mask out any pixels which are outside our region of interest.

####### 6) Use the Hough transform to determine line segments corresponding to lane markings
We now have pixels which are inside our region of interest, and are also regions of high gradient in the image. We want to now remove any pixels which do not have other pixels around them to form a lane. This is achieved by transforming each pixel to a line in the parameter space. Areas of the parameter space which have multiple intersections all correspond to pixels on the same line in the image. This gives us a good estimate of not only the lane marking pixels, but also smaller line segments which can be used to define an equation of a line. Also, since these lines all have slope, we can use the slope information to segment the lane segments as either corresponding to a left lane line segment, or right lane line segment.

####### 7) Linear Least Squares
Although having a series of line segments is useful, what is more useful is having a single equation of a line to be used to describe the lane. In order to achieve this, each line segment is converted into an amount of `(x,y)` points corresponding to the lane length. Using linear least squares, a line of best fit is found to the data points.

####### 8) Plot the lines
Finally, now that a line of best fit for each lane has been determined, the lane lines are plotted inside the region of interest.

### Some example photos and videos can be found here:

### Shortcomings of this method
There are three main shortcomings to this method which we describe here:
1. The region of interest in this method is fixed. A more appropriate method would have a moving region of interest around the previous lane detection. The shortcomings of a fixed region of interest become apparent of curved roads.
2. This method uses an equation of a line to fit the lane markings. Although lane markings are mostly linear for highway driving, this is not always the case, which can lead to a bad detection
3. This method uses a pure detection scheme. That is, it does not take into account previous lane information. Since lanes are continuous, using previous lane information could greatly improve the pipeline and remove spurious detections.

### Suggestions for improvement
We propose three main areas of improvement which are closely related to the shortcomings mentioned above
1. A moving region of interest about the previous lane can be used.
2. A higher order polynomial can be used as the lane model
3. A Kalman Filter, Extended Kalman Filter, or Particle Filter could be used to smooth lane detection variations
