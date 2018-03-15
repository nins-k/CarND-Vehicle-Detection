## Udacity SDCND - Term 1, Project 5

### **Vehicle Detection** ###
---


### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.

This file *__is__* the Writeup Report.

---
---

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The HOG features are extracted using the Udacity classroom code in the method **get_hog_features()** defined at `Cell 05`. 

The method is called with the following parameters:
* Orientation = 9
* Pixels per cell = (8, 8)
* Cells per block = (2, 2)
* Block Normalization = L2-Hys

---

For processing the training data, the **extract_features()** method defined at `Cell 08` is called. It is responsible for:
1. Converting image to _YCrCb_ color space, `Line 17`.
2. Extracting spatial bin features of the image, `Line 31`.
3. Extracting the color historgram features of the image using **color_hist()** method, ` Line 34`. 
4. Extracting the HOG features for one or all of the channels using the **get_hog_features()** method mentioned above, `Line 38`.

---

#### 2. Explain how you settled on your final choice of HOG parameters.

The parameters for HOG and other features are specified in `Cell 13`.

```python
colorspace = 'YCrCb'
orient = 9
pix_per_cell = 8
cell_per_block = 2
hog_channel = 0
hist_bins = 32
spatial_size = (32, 32)
```

Experimenting with different color spaces, I found that *YUV* and *YCrCb* spaces give the best results. Isolating the Y channel gives slightly better test accuracy and fewer false positives, so I've utilized only the Y channel while extracting the HOG features.

Further, resizing down to (32, 32) still maintains enough information to detect the vehicles correctly. 

Lastly, I have left the *orientation* count at 9 because increasing did not result in any significant increase in accuracy.

The total feature length comes out to be **4932**.

#### 3. Describe how  you trained a classifier

The features were normalized using the **StandardScaler()** at `Cell

The training is done in `Cells 15 - 19`.

I have attempted a GridSearch using both **SVC** and **LinearSVC**. I was able to get a much better model using SVC. The results of the GridSearch can be found at `Cell 16`.

```
The best score is 0.9932 with :

SVC(C=10, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape='ovr', degree=3, gamma='auto', kernel='rbf',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.05, verbose=False)
 ```
  
The SVC is initialized and train with these optimum hyper-parameters at `Cell 17` and returns a good test accuracy.
  
  ```
163.63 Seconds to train SVC...
Test Accuracy of SVC =  0.9935
```

---
---

### Sliding Window Search

#### 1. Describe how you implemented a sliding window search. 

Witih a few modifications, I have utilized the **find_cars()** method from the Udacity classroom. It is defined in `Cell 24`. 

This method uses the subsampling approach to extract HOG features. It makes a prediction on the features and returns the bounding boxes with positive predictions.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
---
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![alt text][image6]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image7]


---
---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

