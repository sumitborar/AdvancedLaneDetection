## Writeup Template
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

[image1]: ./project_images/camera_calibration.png "Camera Calibration"
[image2]: ./project_images/undistorted_images.png "Undistorted Image"
[image3]: ./project_images/color_thresholds.png "Color Thresholds"
[image4]: ./project_images/prespective_transform.png "Prespective Transform"
[image5]: ./project_images/lane_detection.png "Warp Example"
[image6]: ./project_images/test_image_lane_output.png "Lane Output"
[image7]: ./examples/example_output.jpg "Output"
[video1]: ./project_video_out.mp4 "Video"


### Submitted Files
1. Advanced Lane Detection.ipynb - Code of running project
2. README.md - Project Rubic
3. project_video_out.mp4 - Output video
4. challenge_video_out.mp4 - Challenge output video

### Camera Calibration

##### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:


![alt text][image1]


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

In the pipeline, as first step each image is passed to the undistort function along with the distortion coefficients and camera matrix calculated eariler duing camera calibration

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Code for color transforms and gradients to create a binary mask is described in section 3 of the ipython notebook.

Color Thresolding - Each image is converted to HSV space and yellow and white lane thresholds are used to detect those respectively. Along with that each image is converted to HLS and H and S channel mask are used for lane detection. I also use RGB mask for lane detection. All the masks are combined to form the final mask.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
    src = np.float32([
            [580, 460],
            [700, 460],
            [1040, 680],
            [260, 680],])

    dst = np.float32([
            [260, 0],
            [1040, 0],
            [1040, 720],
            [260, 720],])
```

This resulted in the following source and destination points:

| Source        | Destination   |
|:-------------:|:-------------:|
| 585, 460      | 260, 0        |
| 700, 460      | 1040, 0      |
| 1040, 680     | 1040, 720      |
| 260, 680     | 260, 720        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Code to identify lane-line pixels is in block 5. I am detecting lane lines using sliding window process. A histogram clearly identifies lane lines on transformed binary mask. Also for subsequent frames, I speed up the process by looking at the previous frames lane points and searching for new lane points in only that area.


![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Code to calculate radius of the curvature and cars position on road is on block 6 of the notebook.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

 Here is an example of my result on a test images:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

 1. [link to my video result](./project_video_out.mp4)
 2. [link to challenge video](./challenge_video_out.mp4)
---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

1. Current approach is unable to detect tracks on the harder challenge track. A more robust approach is needed.
2. I have not implemented check to detect distances between the lanes. That seems like a very relevant check.
3. Another idea I had was around calculating a triangle with center of car's hood as one vertex and another two vertices on the two lanes a certain distance away. Now we could calculate the angles and regardless of the turns or straight road, these angles would remain same and would help in keeping the car on center of the lane.
