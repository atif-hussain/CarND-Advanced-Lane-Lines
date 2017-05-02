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

[image1]: ./output_images/original.jpg "Original"
[image2]: ./output_images/undistorted.jpg "Undistorted"
[image3]: ./output_images/thresholded.jpg "Thresholded"
[image4]: ./output_images/warped.jpg "Warped"
[image5]: ./output_images/polylines.jpg "Polylines"
[image6]: ./output_images/final.jpg "Final"
[video1]: ./project_video_augmented.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

To demonstrate the working, i take a sample road image, and run it through the pipeline,

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the third code cell of the IPython notebook located in `./Proj4.ipynb` .  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained the `calibration matrix, calib.mtx` and `distortion correction calib.dist`: 


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

In fourth cell, I applied the distortion correction to the input image, getting result like this:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (fifth code cell in `./Proj4.pynb`).  Here's an example of my output for this step.  This step is very sensitive to thresholds and requires a lot of tweaking. I took guidance from peer solutions for the same. 

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform created a class called `class Warper`, which appears in the 6th code cell of the IPython notebook.  The `class Warper` takes source (`src`) and destination (`dst`) points, to create the perspective transformation from and to the perspective view. 
I chose the hardcode the source and destination points in the following manner:

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
|  580, 460     |  260, 0       | 
|  700, 720     | 1040, 0       |
| 1040, 720     | 1040, 720     |
|  260, 460     |  260, 720     |


`cv2.warpPerspective` was used to transform the images (`img`). I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In 7th code cell, I used the `numpy.polyfit()` function to fit my left and right lane lines with a 2nd order polynomials, of the kind: 

` self.left_fit[0] * (nonzeroy ** 2) + self.left_fit[1] * nonzeroy + self.left_fit[2] `
` self.right_fit[0] * (nonzeroy ** 2) + self.right_fit[1] * nonzeroy + self.right_fit[2] `


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

From the coefficients of lane fit polynomial, a little mathematical calculations generate the radius of curvature (in 8th code cell in the IPython notebook). 

![alt text][image5]

#### 6. Provide an example image of your result plotted back down onto the road suc


In the 9th code cell of the IPython notebook, we plot the resulting lane (converted to perspective view) on to the image. Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_augmented.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I used the approach described in the tutorial, with minor tweaking of the parameters. 
The pipeline can fail in cases with shadows on road affecting the colors and edges on the road. It also assumes that the road is flat, which can be a concern on hilly terrain. 