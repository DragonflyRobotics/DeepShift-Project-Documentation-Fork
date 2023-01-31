# MAGIST Object Detection Basics
MAGIST uses a multi-layer Convolutional Neural Network (CNN) to efficiently detect and frame relevant objects. The conventional methods to do object *detection* (not recognition) are as follows:

- **YOLO** --> "You Only Look Once" uses a combination of IOU (intersection over union), bounding box regression, and residual blocks.
- **RCNN/Faster-RCNN** --> Uses a region proposal network to create regions of interest and a CNN to find the region with the highest confidence of the predicted object to be entirely bounded.

Due to the input data restrictions (imposed by the definition of general intelligence), these techniques are unusable. MAGIST runs an algorithm that is a combination of YOLO's grid system and a *"temperature"* based detection system for pose estimation.

## Working Principle

MAGIST Object Detection works in 3 main phases:

1. Splitting input images into smaller segments (follows grid-like shape with no overlapping regions)
2. Training and predicting on each individual segment 


![Overview of Object Detection Process](object_detect_proc){ align=center }

