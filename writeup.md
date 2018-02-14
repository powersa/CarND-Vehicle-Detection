## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/search_window.png

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in cell 305 of the IPython notebook. I augmented my data set with images extracted from [Data Set 2](https://github.com/udacity/self-driving-car/tree/master/annotations). The code I used to do so is available in: [data-extraction.ipynb](./data-extraction.ipynb)

In cell 340, I construct a list of vehicle and non-vehicle images. I pass this list to the `extract_features` method, which loads the images, converts the color space and gets features from `extract_features_from_image`. Here's an example of a car:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`). In the end, I found my model performed well with features from all 3 YCrCb color channels, 9 orientations, 8 pixels_per_cell and 2 cells_per_block.

#### 2. Explain how you settled on your final choice of HOG parameters.

In the end, I found my model performed well with features from all 3 YCrCb color channels, 9 orientations, 8 pixels_per_cell and 2 cells_per_block.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

After I had all my features, I created labels and split into a training and test set. I then fit a StandardScaler to my training features in cell 341. I applied that normalization to training and test data before passing it through a LinearSVC in cell 345. This model had a test accuracy of 0.8975. Before I augmented my data, this accuracy was actually higher, but I think that was the result of overfitting.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I had 2 approaches to sliding window search... The first searches the full frame. The second searches targeted regions that contain prior detections.

My window search implementation is available in cell 398.

I found that searching at 4 scales worked to classify multiple portions of cars in the frames.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I found that my classifier produced many false positives while searching the frames. To combat this, I implemented a `Detection` class in cell 762. I used this class to store thresholded labels created from each frame. I then ran a second threshold on the last n detections before classifying a region as a car.

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output_c.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I found that combinging labeled regions from multiple frames of the video was a very good way to reduce false positives. False positives generally do not exist across multiple frames. However, car detections do. This was the single most important way that I filtered out incorrect predictions made by my classifier.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

About halfway through the project, I realized that I had major issues with feature extraction. My model performed very poorly the classified video frames. It took a lot of work to track the bug down. The issue was a mismatch between training feature extraction and feature extraction performed by my pipeline.

Going through this process, I truly appreciate the power of visualization for debugging purposes. Improvements to my pipeline accelerated after I started debugging with images like:

![alt text][image2]

If I continue working on the project, I'd like to improve my classifier and make improvements to components that combine multi-frame detections.
