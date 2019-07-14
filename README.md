## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

### README

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

[image1]: result/undistorted.png "Undistorted"
[image2]: result/thresholded_image.png "thresholded image"
[image3]: result/corners_unwarp.png "corners unwarp"
[image4]: result/partly_lanes.png "Lanes"
[image5]: result/fit_polinomial.png "Fit polinomail"
[image51]: result/fit_polinomial_2.png "Fit polinomail"
[image6]: .result/lane_detected.png "Lanes unwarp "
[video1]: out.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

#### Calibration
 the calibration operation and the result  is saved as pickle file. It can easyly optained if needed. Calibration images are saved in `output_images/camera_cal` folder. after obtaining the camera parameters  for camera calibration and applying sample image and below is the result
 ![alt text][image1]
 
 
 

 


### How to run ### 

The result of the project can be seen in `LaneLineDetection.ipynb` notebook. Neccesary explanations can seen on cell descriptions.
This will take the raw video file at 'project_video.mp4', and create an annotated output video at 'out.mp4'. 
in the notebook there include also step by step the result of the code.






#### 2.Thresholding 

In the above script, I used multiple methods of edge detections to detect the edges from the image and used the masking function to avoid having any noises.

Here are 5 methods that I used for edge. 


* Sober Operator x-direction
* Sober Operator y-direction
* Direction of Gradient
* Magnitude of Gradient
* HLS Color Threshold

different combinations are checked and sattled on combination below
```combined[ (x_binary == 1) | ((mag_thresh_img == 1) & (dir_binary == 1)) | (s_condition )  ] = 1``` 

![alt text][image2]
 


#### 3. Perspective transform.

Now after that the result can be used for obtaining bird eye view. Perspective transform operation is applied to sample images. I try to obtain an automatic method which based on the lane lines extreme values.  But Since height of the optained lane line can be changed I quite to tyring this method.   


I decide to use fixed points instead. since the line must be paralel the point choosed accordingly.



![alt text][image3]




Below is the cordinate points accordingly. 
```
src = np.float32([[220, 720],[580, 460],[710, 460],[1100, 720]])
offset = 200
# 4 destination points
dst = np.float32([[src[0][0]+offset, 720],[src[0][0]+offset, 0],[src[-1][0]-offset, 0],[src[-1][0]-offset, 720]])


```

![alt text][image4]



#### 4. Find Polinomial 

After dividing the image into two part each part contains lanes separetly. Optaining the poins and giving the point to open cv fitpolinomal function give the neccasary parameters.
The code starting point is `fit_polynomial` function. Another search polinomail function is implemented for already existing parameters. 
 

![alt text][image5]

The fucntion takes the parameters and search with certain trashhold. 

![alt text][image51]

#### 5. Radius of curvature of the lane and the position of the vehicle with respect to center.

 `measure_curvature_pixels` function is calculating the curvature. The method return the curvature and it can easyly be translated to real world dimentions if pixels/meter ratio is know. 
 ```xm_per_pix = 3.7/700 # meters per pixel in x dimension ```
 
 I optained bottom point of the extention of the lanes in image  and distance between the center of the undistorted image and the lanelines extention. The code can be seen below. 
 
 ```

bottom_x_left = left_fit[0]*(bottom_y**2) + left_fit[1]*bottom_y + left_fit[2]
bottom_x_right = right_fit[0]*(bottom_y**2) + right_fit[1]*bottom_y + right_fit[2]
vehicle_offset = undist.shape[1]/2 - (bottom_x_left + bottom_x_right)/2
 ```
 
 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.



![alt text][image6]

---

### Pipeline (video)

#### 1. 
all the methods are applyed to video and fallowing result is optained. 


Here's a [link to my video result](./out.mp4)

![alt text][video1]

---

### Discussion

The model is prune to daylight, shadow, reslections etc. That's why cannot be used in real world applications. It is also can easyly be fooled. For further improvement the overtime method can be updated which is only takes `n` avarage of the detected lines. 



