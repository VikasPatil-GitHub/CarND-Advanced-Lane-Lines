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

[image1]: ./output_images/Q1_output.png "Undistorted"
[image2]: ./output_images/Q2_output.png "Road Transformed"
[image3]: ./output_images/Q3_output.png "Binary Example"
[image4]: ./output_images/Q4_output.png "Warp Example"
[image5]: ./output_images/Histogram.png "Smoothened histogram"
[image6]: ./output_images/Q6_output.png "Output"
[video1]: ./processed_project_video.mp4 "Video"

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first and second code cell of the IPython notebook located in "./pipeline.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image in code cell 3 of the IPython notebook located in "./pipeline.ipynb".  Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which appears in the 4th code cell of the IPython notebook.  The `warp()` function takes as inputs an image (`img`) and an additional parameter to indicate normal and inverse perspective transform.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([corners[0],corners[1],corners[2],corners[3]])
dst = np.float32([corners[0]+offset,new_top_left+offset,new_top_right-offset ,corners[3]-offset])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 188, 720      | 338, 720        | 
| 576, 460      | 338, 0      |
| 706, 460     | 976, 0      |
| 1126, 720      | 976, 720        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code for lane lines identification includes a function called `get_LineFit()`, which appears in the 7th code cell of the IPython notebook. This function takes the warped binary image as the input, identifies the lane lines and returns the left and right line polynomial for further processing. The first step in the pipeline is to identify the lane lines; this is done by using histogram and sliding window approach to find the lane lines in the binary image, since the warped binary image contains only the lane line information. The histogram is smoothened using 'scipy.ndimage.filters.gaussian_filter()' to avoid identifying the jitter. The output of the smoothened histogram is shown below.

![alt text][image5]

The two prominent peaks in the histogram indicate the lane lines position in the image. This process is used to identify the lane lines in the given warped binary image using the sliding window approach where the histogram process is repeated different divided sections of the image to identify the lane line pixels and use this information to get the best fit polynomial for the identified lane lines. 

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for lane lines identification includes a function called `detect_lanelines()`, which appears in the 8th code cell of the IPython notebook. This function takes the warped binary image as the input, fills the region between the lane lines, calculates the radius of curvature and offset value with respect to center and finally returns it for further processing.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code for the pipeline output on an image is in 9th and 10th code cell of the IPython notebook. The output of the pipeline is shown below:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's the [output video][video1]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline can be made more robust in the following sections:

* Improving the binary_image() function to further improve the lightness gradient.
* Improving the warp() function to automatically detect the 'src' points instead of hradcoding the the values.
* Include tracking mechanism in the pipeline to improve the speed and perform sanity checks.
