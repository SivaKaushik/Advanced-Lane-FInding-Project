## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first function Calibrate camera of the IPython notebook located in "./CarND-Advanced-Lane-Lines/Advanced Lane Finding.ipynb" .  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]
After Camera Calobration the next function is cal_undistort,which takes in image and returns an distortion corrected image. I use the distortion coefficient,calibration matrix found in the camera calibrate function.
here is the example of undistorted image.
#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of saturation and light in HLS color space  and sobelx thresholds to generate a binary image. thresholding steps start at the begining of function pipeline.I first calcuate the sobelx thersholds by testing out at various thershold values then i calculate saturation thresholds and the lightness. Then i create a combined binary image where all the three parameters are satisfied.  Here's an example of my output for this step.  

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perpective_transform()`, which appears after the function the pipeline,which 5th code cell in the Notebook. The `perpective_transform()` function takes as inputs an image (`img`)which can either be a thersholded image or a normal colour image.  I chose a predefined the source and destination points in the following manner:


| Source        | Destination   | 
|:-------------:|:-------------:| 
| 490, 482      | 0, 0        | 
| 810, 482      | 1200, 0      |
| 1200, 720     | 1200, 720      |
| 200, 720      | 200, 720        |


![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did a sliding window search method to find out the lane lines you can find the code in the 7th code cell in the notebook with the function name find_lane_pixels() after which i fitted the 2nd order polynomial by using the function fit_polynomial() in the same cell. Where in i use the lane pixels found in the find_lane_pixels and use polyfit function to find out the 2nd order polynomial.Here is a image of the sliding window search method with a 2nd order polynomial fitted to it.

![alt text][image5]
After doing the initial search for the lane pixels(lines),i used the finding lane search from prior method where in it only looks at the places where there is a possibilty of lines rather than every where in the image. and the part where it searched is highlighted with green shaded area in the image.This part is in code cell 9 code cell with the function name being "search around poly()". Here again we calculate the 2nd order polynomial using the new lane pixels found.
here is image of it:

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines code cell 11. I took the left lane pixels as well as the right lane pixels and using the radius of curvature formula given in the Course. I found the Radius of curvature. 
I calculated the car position with respect to the center by calculating the midpoint in the image and then calculating the mid point between the left lane and the right lane the car is in. and taking the differnce between them we get the location of the from the center. BUt in order to get the distance in the units we use as metres we convert the pixel values to metres using the standard conversion shown in the course. This part is done in cade cell no.13 with a function named "car_offset()". 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines in code cell no.15 in the function `fill_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

The Project solution video is provided in the name of Project Video Solution.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I faced problem in finding the curvature of the lane.
My code takes a long time to implement on a video i didnt exactly know the reason, but that's a shortcoming i faced and it can be a potential part for it to fail. 
As far as the implementation goes i can fail when there difference in lighting condition like when there is shade falling over the lanes my pipeline might not be able to detect the lanes properly.
I think this can be overcome by writing a code where in our thresholds are aligned more for these lighting conditions and only run at these conditions,and the code must be able to indentify these conditions of lighting and then run a code particularly for it. That might work but i think its too complicated.
