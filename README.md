# Vehicle Detection
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


---

**Vehicle Detection Project**

The goals / steps of this project are the following:


* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Here also apply a color transform and append binned color features, as well as histograms of color, to the HOG feature vector.
* For those first two steps, normalize the features and randomize a selection for training and testing.
* Implement a sliding-window technique and use the trained classifier to search for vehicles in images.
* Run the pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.


[//]: # (Image References)
[image1]: ./examples/examplesAndHogs.png
[image2]: ./examples/multiScaleWindows.png
[image3]: ./examples/testImagesOutput.png
[image4]: ./examples/test4.jpg
[image5]: ./examples/test4Heatmap.png
[image6]: ./examples/labelsMap.png
[image7]: ./examples/finalImage.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the code cell under "Hog Features" of the IPython notebook .  

I started by reading in all the `vehicle` and `non-vehicle` images.

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image1]

#### 2. Explain how you settled on your final choice of HOG parameters.

The parameters are tuned and adjusted manually to maximize the accuracy and minimize the training time.

In the end, I decided not to use the color features as they increased the feature vector dramatically whereas didn't improve the accuracy a lot, also it will increase the training and classifying time.

Below is the parameters I finally used to train and classify:

|Parameter|Value|
|:--------|----:|
|Color Space|YUV|
|HOG Orient|11|
|HOG Pixels per cell|16|
|HOG Cell per block|2|
|HOG Channels|All|
|Spatial bin size| (32,32)|
|Histogram bins|32|
|Classifier|LinearSVC|


### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to restrict the search window to the bottom half the image, and use multi scale windows to search. Apply smaller search windows for further field in view, and bigger search window for nearer field in view.  The search window looks like this:

![alt text][image2]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on four scales using YUV 3-channel HOG features in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image3]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

One thing tricky here is that the video frames are not changing a lot frame from frame, so I created a class to store the detected rectangles and only keep the latest max_num_frames, so the apply the heatmap and the folloing steps to the average of the past max_num_frames frames.

Here's an example result showing the heatmap from a test image, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the test image:

### Here are the test image and its corresponding heatmap:
![alt text][image4]
![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the image:
![alt text][image7]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

From the result we can see, sometimes it still predict false positives or smaller boxes for cars where it really appears. All this could lead to problems if we apply this algorithm to real autonomous driving cars. Some possible solutions includes:
* investigating different HOG parameters
* investigating different color spaces
* combine color Features
* tuning on the search windows.
