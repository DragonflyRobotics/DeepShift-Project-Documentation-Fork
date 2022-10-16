# MAGIST Object Detection Basics
MAGIST uses a multi-layer Convolutional Neural Network (CNN) to efficiently detect and frame relevant objects. The conventional methods to do object *detection* (not recognition) are as follows:

- **YOLO** --> "You Look Only Once" uses a combination of IOU (intersection over union), bounding box regression, and residual blocks.
- **RCNN/Faster-RCNN** --> Uses a region proposal network to create regions of interest and a CNN to find the region with the highest confidence of the predicted object to be entirely bounded.

Due to the input data restrictions (imposed by the definition of general intelligence)
