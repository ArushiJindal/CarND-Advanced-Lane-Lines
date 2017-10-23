
#Project - 4 : Advanced Lane Finding

The goal of this project is to develop a pipeline to process a video stream from a camera mounted on the front of a car, and produce an output which identifies
1. The lanes
2. Curvature of road
3. The location of vehicle relative to centre

Broadly, the following steps are done
1. Compute the  camera calibration matrix and distortion coefficients given a set of chessboard images.
2. Apply a distortion correction to raw images.
3. Use color transforms, gradients, etc, to create a thresholded binary image, representing the lane lines.
4. Apply a perspective transformation to warp the image to a birds eye view perspective of the lane lines.
5. Find the lane line pixels and fit a second order polynomial to the boundaries.
6. Determine the curvature of the lane and vehicle position with respect to center.
7. Warp the detected lane boundaries back onto the original image.
8. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.


# The Complete Pipeline

## Camera Calibration

OpenCV functions findChessboardCorners and drawChessboardCorners are used to identify the corners on multiple chessboard images taken from different angles.

These locations are then used in another OpenCV function calibrateCamera  to compute the camera calibration matrix and distortion coefficeints.

![image.png](attachment:image.png)

## Distortion Correction

This camera calibration matrix and distortion coefficeints are used with OpenCV function undistort to remove the distortion from images.

![image.png](attachment:image.png)


# Creation of thresholded binary image


This code is part of functions abs_sobel_thresh, mag_thresh, dir_threshold, hls_select. I used combination of H and L channel color thresholds, and sobel gradients, direction and magniture ofgradients. 

![Capture2.PNG](attachment:Capture2.PNG)



# Perspective Transform (To create a bird's eye view of the image)


The code is in corners_unwarp function. I chose one sample image and plotted the src and dst points on that sample image to be reference for the transformation.

![image.png](attachment:image.png)

![3.PNG](attachment:3.PNG)

# Find the left and right lanes, fit polynomial, determine Radius of Curvature and the Position of Vehicle

The code given by Udacity is used for this step. As an input, the image obtained after perspective transform and binary thresholds is used. In order to fit a polynomial to lanes, the steps carried in order are (function fill_LaneLines):

1. Take a histogram along all the columns in the lower half of the image
2. Find the peak of the left and right halves of the histogram
3. Identify the x and y positions of all nonzero pixels in the image
4. Fit a polynomial to each lane using the numpy function numpy.polyfit()
5. To calculate the radius of curvature of each lane line in meters, Udacity provided reference is used.
6. Calculated the average of the x intercepts from each of the two polynomials position = (rightx_int+leftx_int)/2
7. Calculated the distance from center by taking the absolute value of the vehicle position minus the halfway point along the horizontal axis distance_from_center = abs(image_width/2 - position)

The polynomial fitted above is ploted on a warped image. Also the space between the polynomials is filled in to shpw the lane car is currently in. Minv is used to unwrap the image back to original from bird's eye view. This distance from centre and radius of curvature is printed on the final image.

![image.png](attachment:image.png)

# Video Processing

The above formulations are extended to be able to process the video frame-by-frame.

In the video pipeline two classes are created for left and right lanes respectively. In this pipeline, firstly I check whether lane is detected in rpevious frame. If it was, then it only checks lane pixels which are in close proximity to the polynomial calculated in the previous frame. This way, processing is faster as no need to scan through the entire image.

Look-Ahead Filter :-
If at any time, the pipeline fails to detect lane pixels based on the the previous frame, it will go back in to blind search mode and scan the entire binary image for nonzero pixels to represent the lanes.

Smoothing :- 
Line detections will jump around from frame to frame a bit and it is preferable to smooth over the last n frames of video to obtain a cleaner result. I chose to average the coefficients of the polynomials for each lane line over a span of 10 frames.


(result_full.mp4 provided)

# Possible Limitations

Lane lines are detected fairly good in the test video, however results on challenge video are not good. For a consistent weather, and road conditions the algorithm is quite robust. 
The pipeline needs to be improved to cater for differential conditions of lighting, weather, roads, lanes, overpass, different shadows etc.
