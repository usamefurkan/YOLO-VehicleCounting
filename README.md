# Yolo Vehicle Counter in Restricted Area

## Overview
You Only Look Once (YOLO) is a CNN architecture for performing real-time object detection. The algorithm applies a single neural network to the full image, and then divides the image into regions and predicts bounding boxes and probabilities for each region. For more detailed working of YOLO algorithm, please refer to the [YOLO paper](https://pjreddie.com/media/files/papers/YOLOv3.pdf). 
The project aims to count vehicles restricted area (motorcycle, bus, car, cycle, truck, train) detected in the input video using YOLOv3 object-detection algorithm.

## Working 

When detected vehicles enter the restricted area, it is added to the count. Vehicles leaving the restricted area are subtracted from the number. After getting detected once, the vehicles get tracked and do not get re-counted by the algorithm. 

You may also notice that the vehicles will initially be detected and the counter increments, but for a few frames, the vehicle is not detected, and then it gets detected again. As the vehicles are tracked, the vehicles are not re-counted if they are counted once. 


## Prerequisites
* Linux distro or MacOS (Tested on Ubuntu 18.04)
* A street video file to run the vehicle counting 
* The pre-trained yolov3 weight file should be downloaded by following these steps:
```
cd yolo-coco
wget https://pjreddie.com/media/files/yolov3.weights
``` 

## Dependencies for using CPU for computations
* Python3 (Tested on Python 3.6.15)
```
sudo apt-get upgrade python3
```
* Pip3
```
sudo apt-get install python3-pip
```
* OpenCV 3.4 or above(Tested on OpenCV 3.4.2.17)
```
pip3 install opencv-python==3.4.2.17
```
* Imutils 
```
pip3 install imutils
```
* Scipy
```
pip3 install scipy
```

## Dependencies for using GPU for computations
* Installing GPU appropriate drivers by following Step #2 in the following post:
https://www.pyimagesearch.com/2019/12/09/how-to-install-tensorflow-2-0-on-ubuntu/
* Installing OpenCV for GPU computations:
Pip installable OpenCV does not support GPU computations for `dnn` module. Therefore, this post walks through installing OpenCV which can leverage the power of a GPU-
https://www.pyimagesearch.com/2020/02/03/how-to-use-opencvs-dnn-module-with-nvidia-gpus-cuda-and-cudnn/

## Usage
* File paths are specified. File paths must be changed for different files
* The project is prepared in jupyter notebook. Also .py file is available


Examples: 

* Running with defaults
```
inputVideoPath=os.path.sep.join(["./videos/cars.mp4"]) 
labelsPath= os.path.sep.join(["./yolo-coco/coco.names"])
LABELS = open(labelsPath).read().strip().split("\n")
weightsPath = os.path.sep.join(["./yolo-coco/yolov3.weights"])
configPath = os.path.sep.join(["./yolo-coco/yolov3.cfg"])
outputVideoPath = os.path.sep.join(["./outputVideos/VehicleCounting.avi"])
USE_GPU=0
```
* Using GPU
```
USE_GPU=1 
```

## Implementation details

* The detections are performed on each frame by using YOLOv3 object detection algorithm and displayed on the screen with bounding boxes.
* The detections are filtered to keep all vehicles like motorcycle, bus, car, cycle, truck, train. The reason why trains are also counted is because sometimes, the longer vehicles like a bus, is detected as a train; therefore, the trains are also taken into account.
* The center of each box is taken as a reference point (denoted by a green dot when performing the detections) when track the vehicles.   
* Also, in order to track the vehicles, the shortest distance to the center point is calculated for each vehicle in the last 10 frames. 
* If `shortest distance < max(width, height) / 2`, then the vehicles is not counted in the current frame. Else, the vehicle is counted again. Usually, the direction in which the vehicle moves is bigger than the other one. 
* For example, if a vehicle moves from North to South or South to North, the height of the vehicle is most likely going to be greater than or equal to the width. Therefore, in this case, `height/2` is compared to the shortest distance in the last 10 frames. 
* As YOLO misses a few detections for a few consecutive frames, this issue can be resolved by saving the detections for the last 10 frames and comparing them to the current frame detections when required. The size of the vehicle does not vary too much in 10 frames and has been tested in multiple scenarios; therefore, 10 frames was chosen as an optimal value.
* Vehicles are counted when green dot in the center of the vehicles cross the red line (i.e. enter the restricted area) in the sample video. If it crosses the orange line (i.e. leaves the restricted area) it is subtracted from the number. 
* The coordinates of the lines should be determined according to the region where the vehicle count will be made. The position of the lines is important because the detection of vehicles by perspective is low in some cases. 
* In the example video, as you move away from the camera, the detection of vehicles becomes more difficult. It is important that the area to be counted is in an appropriate perspective. 
* By looking at the first frame, it is determined how many vehicles are in the restricted area at the beginning.
## Reference
* https://www.pyimagesearch.com/2018/11/12/yolo-object-detection-with-opencv/ 
