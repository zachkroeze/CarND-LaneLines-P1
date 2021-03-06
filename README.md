# **Finding Lane Lines on the Road**
This repository is for the first project of the Udacity Self-Driving Car Engineer Nanodegree Program. The project involves using computer vision techniques to find lane markings on the road. This document gives an overview of the lane detection pipeline used to annotate both images and videos.

### Lane Finding Pipeline
The proposed lane finding pipeline consists of several steps which we describe here:

* **Convert image to grayscale:**
We begin the pipeline by converting the image to grayscale. We do this since colour alone does not give us a clear indication of a lane or non-lane pixel. By converting to grayscale we can focus on intensity, which is a better predictor.

* **Apply Gaussian smoothing:**
We apply Gaussian smoothing since images are inherently noisy. We want to smooth out this noise so we can better detect gradients in the next step.

* **Apply Canny edge detection:**
Canny edge detection is a classic computer vision technique which uses the gradient of the image to detect edges. In the case of lane detection, we expect to find large gradients when transitioning from a non-lane pixel to a lane pixel, and vice versa. We will likely also pick up other edges in our image, but this will be handled by the other steps in our pipeline. 

* **Search only on a region of interest:**
This step relies on the fact that the camera position on the vehicle remains constant, and therefore the region of the road in which we would like to search for lane markings is relatively fixed. We mask out any pixels which are outside our region of interest.

* **Hough transform to determine line segments:**
We now have pixels which are inside our region of interest that also have high gradient in the image. We only want to keep the subset of these pixels which can be fit to line segments using neighboring pixels. This is achieved by transforming each pixel to a line in the parameter space. Areas of the parameter space which have multiple intersections all correspond to pixels along the same line in the image. This gives us a good estimate of not only the lane marking pixels, but also smaller line segments which can be used to define an equation of a line. 

* **Linear Least Squares (modifying `draw_lines()`):**
Although having a series of line segments is useful, what is more useful is having a single equation of a line to be used to describe the lane. In order to achieve this, each line segment is converted into a sequence of `(x,y)` points. The number of points each line segment generates corresponds to the length of the line segment. We also classify the points as either being a left lane point, or right lane point. This is determined using the slope information (negative or positive) for left or right lanes. Using linear least squares, a line of best fit is found for each lane.

* **Plot the lines:**
Finally, now that a line of best fit for each lane has been determined, the lane lines are plotted inside the region of interest.

### Example photos
Below are the five images which were used to guide the development of the lane detection pipeline. As you can see, on these five images the detection performs perfectly.

![solidWhiteCurve](/test_images_output/solidWhiteCurve.png)
![solidWhiteRight](/test_images_output/solidWhiteRight.png)
![solidYellowCurve](/test_images_output/solidYellowCurve.png)
![solidYellowurve2](/test_images_output/solidYellowCurve2.png)
![solidYellowLeft](/test_images_output/solidYellowLeft.png)
![whiteCarLaneSwitch](/test_images_output/whiteCarLaneSwitch.png)

The videos can be found in the folder `/test_videos_output/`

### Shortcomings of this method
There are three main shortcomings to this method which we describe here:
1. The region of interest in this method is fixed. A more appropriate method would have a moving region of interest around the previous lane detection. The shortcomings of a fixed region of interest are apparent when beginning to analyze curved roads.
2. This method uses an equation of a line to fit the lane markings. Although lane markings are mostly linear for highway driving, this is not always the case, which can lead to a bad detection.
3. This method uses a pure detection scheme. That is, it does not take into account previous lane information. Since lanes are continuous, using previous lane information could greatly improve the pipeline and remove spurious detections.

### Suggestions for improvement
We propose three main areas of improvement which are closely related to the shortcomings mentioned above
1. A moving region of interest about the previous lane can be used.
2. A higher order polynomial can be used as the lane model.
3. A Kalman Filter, Extended Kalman Filter, or Particle Filter could be used to smooth lane detection variations.
