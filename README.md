## Advanced Lane Finding

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

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.   
You're reading it!
### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.
* corrected calibration image.
* Read calibration images
* Generate object points
* Find image points with cv2.findChessboardCorners
* Calibrate the camera and obtain distortion coefficients with cv2.calibrateCamera
![alt text](https://github.com/hyo009/CarND-Behavioral-Cloning-P3/blob/master/images/lcr.png?raw=true "left, center and right images")

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
Apply cv2.undistort with the camera matrix and distortion coefficients obtained.
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/undistorted_output.png)
#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used a combination of color and gradient thresholds to generate a binary image.

First of all, I defined a very simple method thresh() which can be used to apply color threshold in any color space. Then I defined colorthresh() function which takes the red-channel component of image from RGB color space and S-channel component from HLS space. The lanes were detected efficiently in R channel as well as S channel so I chose to combine the pixels detected in both of this channels' thresholds by applying a logical OR operation.

**S-channel threshold**
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/hls.png)
**Red-channel threshold**
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/red.png)
**combine color threshold**
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/colorthresh.png)

The binary file received as output of colorthresh() was then used as an input to Gradient Thresholding method gradthresh(). This method in turns calls different Gradient based thresholding methods which computes gradients along X dimension, Y dimension, Magnitude gradient and Direction Gradient. The various threshold values, kernal size used in the project were somewhat taken from classroom videos plus trial and error.

**grad threshold**
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/gradthresh.png)
**final threshold**
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/combinethresh.png)

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.
* Select the region of interest in source images.
* Set the destination region of transformed images
* Source points and destination points are:

| Source        | Destination   |
|:-------------:|:-------------:|
| 547, 480      | 250, 0        |
| 250, 675      | 250, 675      |
| 1030, 675     | 1030, 675     |
| 733, 480      | 1030, 0       |

* Code the source and destination polygon coordinates and obtain the matrix M that maps them onto each other with cv2.getPerspective
* Warp the image to the new birds-eye-view perspective with cv2.warpPerspective and the perspective transform matrix M we just obtained
* Examples of source images and transformed images
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/warp.png)

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
* Divide the image into 9 horizontal strips (steps) of equal height.
For each step, take a count of all the pixels at each x-value within the step window using a histogram generated from np.sum.
* Find the peaks in the left and right halves (one half for each lane line) histogram with np.argmax.
* Get (add to our collection of lane line pixels) the pixels in that horizontal strip that have x coordinates close to the two peak x coordinates.
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/slide.png)

* Fit a second order polynomial to each lane line using np.polyfit.
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/findlines.png)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.
* I used the example code in Measuring Curvature section to calculate the left and right curvatures. Then I choose the average of left and right curvatures as my center curvature to control steering angles in the future.

* Code in the cell 41 of P4.ipynb.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.
![alt text](https://github.com/hyo009/CarND-Advanced-Lane-Lines-P4/blob/master/output_images/final.png)

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/qUhDga4RJR4/0.jpg)](https://www.youtube.com/watch?v=qUhDga4RJR4)


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
I used all the computer vision techniques discussed in classroom module of P4 except Convolution method. I believe that the pipeline is working fine because of the available day light but in darkness at night or similar whether conditions, it might not be entirely feasible to detect the lane lines using the same exact pipeline with pre-defined threshold values. I think a more targeted approach for lanes detection would be to tune the threshold values dynamically based on different lighting conditions. Another problem is in other countries whose road lane lines are not clear, the pipeline cannot be used. I need to combine other sensors' data to detect lane information.
