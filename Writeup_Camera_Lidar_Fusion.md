## Writeup to address the project rubric points and explanation of the programming script

#### FP.1 Match 3D Objects
- the function 'matchBoundingBoxes' is implemented in the camFusion_Student.cpp

- Using a matrix as a selection map, I count number of occurences of keymatches in different combinations of currFrame bounding box and prevFrame bounding box.

- For every bounding box in currFrame, I checked for the one with most number of keymatches and put them in the 'bbBestMatches' map.

#### FP.2 Compute Lidar-Based TTC
- I am calculating the mean of the lidar points from the current frame and the previous frame. The TTC is calculated as following
```C++
TTC = (meanXCurr * dT)/(meanXPrev - meanXCurr);

```
#### FP.3 Associate Keypoint Correspondences with Bounding Boxes
- The difference in X and Y values are calculated in key points from the current and previous frame.
- The Euclidean distance between the key point matches from the current and previous frame is calculated using the difference value.
- There are two criteria to be satisfied for the key point matches to be part of the bounding box and they are:
1. The key point should be in the region of interest.
2. The distance between the key point matches should be less than the mean euclidean distance.


#### FP.4 Compute Camera-Based TTC
- I have looped over the key points and computed the distance ratio of the current and previous frame.
- I have calculated the TTC as the inverse of median distance ratio.

```C++
TTC = -dT/(1 - median_distance_ratio);

```

#### FP.5 Performance Evaluation 1
- The TTC value of Lidar for the image 12 and 17, negative values is calculated. It signifies that
the relative velocity decreases. However the time value cannot be negative.
Why may have this happened?
- The TTC Lidar for image 7 is much smaller than camera. This may possibly be because the Lidar reflected from a particle.  
- Since it is expected that the X value in two frames will be very close. Suppose if there were some vibrations during the lidar measurements, that will disrupt the whole TTC calculation.
- To make it more robust, we can reduce the variances obtained with both Lidar and Camera, by employing Kalman filter.


#### FP.6 Performance Evaluation 2
- Average TTC Camera is noted down for different combination of detector / descriptor.
- And the difference between the TTC lidar and camera is calculated from the average value and tabled in the following picture.
- Also, when the TTC lidar and camera values are way off, they are ignored in the difference measurement, as it will disrupt the process of estimation of best combination.

##### AVERAGED DIFFERENCE BETWEEN TTC LIDAR AND TTC CAMERA

--> This is table 1 in the excel file 3D_Tracking_Camera_Lidar.xlsx as part of the submitted zip.

##### AVERAGE TIME TAKEN

--> This is table 2 in the excel file 3D_Tracking_Camera_Lidar.xlsx as part of the submitted zip.
In this excel, you can see that the Time To Collision provided by LiDAR and Camera. Based on these results, we can see that HARRIS, ORB, and BRISK are the least performant in term of detection & TTC estimation.
The best were SHITOMASI, AKAZE and SIFT.

- But, to also account for the time taken, I have chosen following three to be my best choices:
    1. Detector - HARRIS, Descriptor - SIFT
    2. Detector - HARRIS, Descriptor - FREAK
    3. Detector - ORB, Descriptor - ORB


- To improve further we could eliminate the outliers by employing the following methods:
    *  A Kalman filter could be employed to reduce the variance.
    *  Increase the number of frames to compute the TTC.
Possible causes of outliers:
    *  Scale change and contrast change, if there is a lighting change between subsequent frames.
    *  keypoint mismatching is possible, for example, there are two similar features in the tailgate of a car (Rear light on both ends). 


