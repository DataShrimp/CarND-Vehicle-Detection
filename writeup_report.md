# Vehicle Detection Project

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # "Image References"
[image1]: ./output_images/car.png
[image2]: ./output_images/notcar.png
[image3]: ./output_images/carhog.png
[image4]: ./output_images/notcarhog.png
[image5]: ./output_images/example1.png
[image6]: ./output_images/example2.png
[image7]: ./output_images/example3.png
[image8]: ./output_images/heat1.png
[image9]: ./output_images/bound1.png
[image10]: ./output_images/heat2.png
[image11]: ./output_images/bound2.png
[image12]: ./output_images/heat3.png
[image13]: ./output_images/bound3.png
[video1]: ./output_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first part of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

![alt text][image2]

I then explored different  `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I then showed images above and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `gray` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image3]

![alt text][image4]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and my final choice of HOG parameters was: `orient=9`, `pix_per_cell=(8,8)`, `cell_per_block=(2,2)`, `color_space=YCrCb`. I extracted hog features from all three color channels and combined them together. 

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using `sklearn.LinearSVC()` function in the second part of IPython notebook. I selected HOG features as well as color spatical features and histgram features. I combined them as the inputs of classifier.  Then I randomized and normalized  them to avoid the overfitting. The test accruracy is 99.3% compared to the accruracy of 94.8% only using hog features. 

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to use multiple-scaled window searching method.  The hog features were once extracted and then were sub-sampled to get all its overlaying windows. Each window is defined by a scalling factor `scale=1.5`. In the end, the range of interesting (`yrange=[400,656]`) is set to further improve the searching efficiency. 

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:



![alt text][image5]![alt text][image6]![alt text][image7]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./output_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I didn't do false positive detection because I found it rarely disappeared in the test video. I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, and the bounding boxes then overlaid:

### Here are three frames heatmaps and their corresponding bounding boxes:

![alt text][image8]![alt text][image9]

![alt text][image10]![alt text][image11]![alt text][image12]![alt text][image13]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In my first trial, I only took part of trained images (about one third of all images). The test accuracy was about 99% but there existed lots of false positive cases. Then I used all images for classifier training, the pipeline was more robust. 

However, when two cars were close, the detected bounding box would contain both of them. And sometimes the bounding box would contain part of the car. I think adding some constraints like the size and orientation of the tracked bounding box will make the result more robust. 

