# SAGIRAC [Simple Algorithm for Getting Image Range And Class]
In this project, we shall create 2 models that will enable our webcam to simultaneously detect targets[purple circle] & predict their distances. \
There are 2 parts to this project: \
The first part is Object Detection. \
This is where we do transfer learning for YOLOv5 using our custom dataset. \
The second part is Distance Estimation. \
This is where we train a linear regression model using our custom dataset. 

Part 1:
Object Detection 

Before we begin, we need to collect 100 images with their distance labels. \
We make things simple, I used a webcam[logitech c920] for taking images & a HC-SR04 ultrasonic sensor for taking distance labels: 
![alt text](https://github.com/kwquan/SAGIRAC/blob/main/project_setup.jpg) 
![alt text](https://github.com/kwquan/SAGIRAC/blob/main/project_setup_2.jpg) 
The ultrasonic sensor uses SONAR to detect image distance by taking into account the speed of sound & time taken for signal to travel from the sensor, bounce off the target & return to the sensor. \
The distance estimations are a bit inaccurate & the sensor is known to over-estimate distances. \
Nonetheless, we shall stick to it for now. 

There are numerous instructional videos on how to set up ultrasound sensor with arduino so I won't go into the details here. \
Webcam is also easy to set up as we only need to plug it into USB. \
I also heavily-discounted the speed of sound in my arduino code to obtain better distance measurements. 

With the set-up, we can proceed to take 100 images with their distance readings. \
Next, we can run the data augmentation code in SAGIRAC.ipynb[in sagirac_dataset folder] to create 1000 augmented images. \
This much data is required in order to ensure our trained models are able to perform well. 

Next, we annotate images using makesense.ai[https://www.makesense.ai](url) \
This is an online tool which allows us to draw the bounding box for each image & download the labels in YOLO format to facilitate transfer-learning. \
All we need to do is to upload images > select object detection > draw bounding boxes > export labels as YOLO format. \
![alt text](https://github.com/kwquan/SAGIRAC/blob/main/annotation_example.png)

A word of caution: this part is very tedious as it involves annotating 1000 images

Once the annotations are done & labels downloaded, one can see that they are text files with rows of 5 numbers:
[class, midpoint_x, midpoint_y, box_width, box_height] expressed as ratio of image dimensions. 

Before proceeding further, save the files in their respective folders for YOLOV5 re-training:

![alt text](https://github.com/kwquan/SAGIRAC/blob/main/files.png)

All images have to be placed in images folder. \
Here, I placed the 1000 augmented images in train, I randomly picked 97 of the original images as val & the remaining 3 as test. \
Similarly, place the downloaded image annotations in the labels folder, under their respective folders[train/val]. \
Remember, we will need to do annotations for the 1000 augmented images & the 97 original images[used for val] as well. 

Once this is done, we are finally ready to re-train YOLOV5 last-layer weights on our custom dataset. \
Run the Model Training part in SAGIRAC.ipynb. \
Remember to run the model training & prediction parts in the command line. \
Once that is done, print out 1 random test image & observe that model performs well on predicting our target.

This concludes Part 1: Object Detection

Part 1:
Distance Estimation

The idea for distance estimation is simple. \
For our target, if bounding box is smaller, the target should be located further. \
However, I decided against using bounding box area as some augmented images contain targets with only half of them appearing[due to our horizontal shift during augmentation]. \
As such, I decided to pick the max of bounding box width & height & use it to predict target distance. 

First, we create 2 CSVs: 

The first CSV is created by looping through image names & getting their respective distance labels. \
Columns will include 'filename' & 'distance'

The second CSV is created from image annotations . \
Columns will include 'filename','box_width' & 'box_height' 

FInally, merge both CSVs on 'filename' column so that each row now represents an image, with it's bounding box dimensions & distance label. \
We also create a 'max dim' column that takes the max of box_width & box_height. 

Before proceeding further, let's check if linear relationship holds true:

![alt text](https://github.com/kwquan/SAGIRAC/blob/main/linearity_check.png)

This graph shows distance plotted against max dim[train images]. \
Although relationship is not exactly linear, a linear equation provides a good estimate for target distance and hence we shall stick to linear regression for now. 
