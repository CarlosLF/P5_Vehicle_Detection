

# Project 5, Vehicle Detection Project

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier

* In addition, we have applied a color transform and append binned color features, to the HOG feature vector. 

* The feature vector is normalized  and a randomized selection for training and testing is performed.

* A sliding-window technique is implemented, and the trained classifier is used to search for vehicles in images.

* The pipline is run on a video stream (we start with the test_video.mp4 and later implement on full project_video.mp4), and a heat map of recurring detections is created frame by frame to reject outliers and follow detected vehicles.

* A bounding box is estimated for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car.png
[image2]: ./examples/not_car.png
[image3]: ./examples/hog.png
[image4]: ./examples/sliding_windows.png
[image5]: ./examples/sliding_window.png
[image6]: ./examples/heat.png
[image7]: ./examples/labels_map.png
[image8]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

This is the writeup

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the second code cell of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images (third code cell of the IPython notebook).  

Here is an example of the vehicle classes:

![alt text][image1]

Here is an example of the non-vehicle classes:

![alt text][image2]


I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image3]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and the final parameters were choosed based on the accuracy of the SVM classifier. I d the classifier with different images and get the best results with the Ycrbcr color space, and the HOG parameters `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using 

svc = LinearSVC()
svc.fit(X_train, y_train)


The code is in cell 10 of the IPhyton notebook.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to start with a single scale of 1.5 and ystart= 400, ystop= 656


![alt text][image4]


But then I use three different scales (detect_vehicles2 function), the first was

ystart = 400,
ystop = 656,
scale = 1.5

The second was 

ystart = 400,
ystop = 500,
scale = 1

And the last one was

ystart = 400,
ystop = 700,
scale = 2


The results of the each searchs is added to box_list.


In addition, I also use a deque object to store the heat matrices of the last 10 frames, these matrices are averaged to compute the heat and reject outliers. 

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Finally, I searched on thre scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image5]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my video result](./project_video_output2.mp4)

Here's a [dropbox link to my video result](https://www.dropbox.com/s/whj9gl1zf7kglhp/project_video_output2.mp4?dl=0)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are three frames and their corresponding heatmaps:

![alt text][image6]

Is important to note that in the detect_vehicles2 function, the heat of the last 10 frames is stored in a deque object, and the final heat is computed with the mean of the heats.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?


The first problem that I faced was the selection of the HOG parameters, I have to use different colors and different parameters on the test images.  Then I faced a problem with the combination of color and spattial binning features. The problem was that I didn't normalize the feature vector.

Another problem was the detection of false positives, but with the use of heat maps this can be reduced. 

With the current pipeline, the algorithm will fail with very dark images. Another problem are the oncoming cars, the cars that appear at the corners have an incomplete image and therefore these images are harder to classify.

Future work:

* I will like to work in speed up the code
* I also will like to add some Kalman filter to make the tracking more stable
* I will also like to explore the combination of histogram equalization

