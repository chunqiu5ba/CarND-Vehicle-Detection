##Projet 5 Writeup
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
[image1]: ./output_images/car_noncar.png
[image2]: ./output_images/car_noncar_hog.png
[image3]: ./output_images/find_car.png
[image4]: ./output_images/heatmap.png
[image5]: ./output_images/test_final.png
[video1]: ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the 2 and 3 code cell of the IPython notebook.

Data used are all GTI and KITTI dataset. As GTI data are time sequence images, I manually seperated images in each folder, first 80% as training and rest as testing. This will decrease overfeeding.
Here are some example images of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `get_hog_featuers()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `get_hog_featuers()` output looks like.

Here is an example using the and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and evaluate the performance on both time speed and result. Finally settled on YCrCb color space, spatical feature, Color Histogram and hog feature. This is in code cell 4.

* color_space: YCrCb
* spatial_size = (16, 16)
* hist_bins = 32
* orient = 9
* pix_per_cell = 8
* cell_per_block = 2
* hog_channel = 'ALL'
* spatial_feat = True
* hist_feat = True
* hog_feat = True

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using all hog channels, YCrCb color space, spatial and color features. Dataset was normalized and shuffled before training, please see code cell 5 and 6. The test accuracy is 0.9842

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I used Hog Sub-sampling Window Search, implemented by a single function find_cars (code cell 7) that's able to both extract features and make predictions. Searching area is limited to y position (400, 656), 3 windows scales (0.9, 1.5 and 2) are used. Instead of overlap, 2 cells per step are used. All above numbers are settled after testing with different numbers on test image to reach out the best detection. 



####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

As described above, I searched on three scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here is an example image:

![alt text][image3]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

On a single frame, heatmap are created by adding up the detected car box, and then threshold are set to filter out false positives. Final box were drew by function `draw_labeled_bboxes`. These code are in cell 9 and here is an example of heatmap threshold and draw box:

![alt text][image5]

For the video pipeline, I used a Window_History class. Class instance "history" will hold detected hot windows from the past 20 frames, then heatmap threshold and draw label box were implemented on top of the "history" instance. This process will make the detector more robust and stable.


---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In this project I carefully divided training and testing dataset between time sequence images, trained classifer with linear SVM, implemented Hog Sub-sampling Window Search for sliding window search, finally used heatmap from historical images to improve the detection accuracy and minimize false positives.
The final resule is satisfied. However, there is still some channlenge left
* When the car is far, a smaller sampling rate has to be used which will also bring in more false positives.
* Bounding box not always closely followed the the car image, there were missing detection on several frames.
* I have read from forum that CNN network like YOLO and SSD were use to tackle this problem. This will be the future enhancement for this project.
