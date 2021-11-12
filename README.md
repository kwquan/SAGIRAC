# SAGIRAC [Simple Algorithm for Getting Image Range And Class]
In this project, we shall create 2 models that will enable our webcam to simultaneously detect targets & predict their distances. \
There are 2 parts to this project:
The first part is Object Detection. \
This is where we do transfer learning for YOLOv5 using our custom dataset. \
The second part is Distance Estimation. \
This is where we train a linear regression model using our custom dataset. \

Part 1:
Object Detection \

Before we begin, we need to collect 100 images with their distance labels. \
We make things simple, I used a webcam[logitech c920] for taking images & a HC-SR04 ultrasonic sensor for taking distance labels: 
![alt text](https://github.com/kwquan/SAGIRAC/blob/main/project_setup.jpg) 
![alt text](https://github.com/kwquan/SAGIRAC/blob/main/project_setup_2.jpg) 
The ultrasonic sensor uses SONAR to detect image distance by taking into account the speed of sound & time taken for signal to travel from the sensor, bounch off the target & return to the sensor. \
The distance estimations are abit inaccurate & the sensor is known to over-estimate distances. \
Nonetheless, we shall stick to it for now. 
