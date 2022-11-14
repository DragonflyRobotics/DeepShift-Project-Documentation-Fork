# Object Detection Prediction

Once the model is trained, you can run predictions on it or use it as a checkpoint to train further at a later point in time. This page requires a trained model (`model.tflite` or `model.h5`).

## Simple Prediction API

The first thing you have to do import the necessary module:

```py linenums="1"
from MAGIST.Vision.FullySupervisedModels.MAGIST_Lite_Detector import MAGIST_CNN, MAGIST_CNN_Predictor
```

Next, you must instantiate the class and provide the configuration file path.

```py linenums="1"
predictor = MAGIST_CNN_Predictor("config.json")
```

!!! important

    If you want the string IDs that correspond to the numerical answer the prediction provides, please look at the "Additional Methods" section 

### Single Image Prediction 

To predict on a single image, you must use the appropriate method and provide the relative (or absolute) path to the image. 