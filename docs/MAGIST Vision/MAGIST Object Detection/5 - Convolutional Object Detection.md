# Convolutional Neural Networks for Object Detection

To build a CNN using the MAGIST API using the functions provided by MAGIST Vision is extremely complicated. Most of the configuration for the model is done through the configuration files ([Learn More](../../Configuration/1 - Setup Config File.md)). Through a default run configuration in the CNN class, you can train the entire model using 1 command. But, to gain finer control over the exact process, you have the option to individually call each subprocess. That is why this page is split into 2 sections: Basic and Advanced.

## Basic
First, you have to import the MAGIST Lite Detector:

``` py linenums="1"
from MAGIST.Vision.FullySupervisedModels.MAGIST_Lite_Detector import MAGIST_CNN, MAGIST_CNN_Predictor
```

!!! warning
    
    At this point, you MUST have your configuration regarding the CNN done in the `config.json` file. If not, the rest of the code will not function correctly.

Now, you must initialize the CNN using the following line:

``` py linenums="1"
cnn = MAGIST_CNN("config/config.json") # (1)
```

1. Make sure to make the local path match your development environment.

The final step is to run it. There are two ways to do this and the latter one is recommended.

#### Execution on main thread (not recommended)

``` py linenums="1"
cnn() # (1)
```

1. The `MAGIST_CNN` class contains an `__call__` method that supports directly calling the class. 

This code will run on your current Python thread meaning that the script will remain on the CNN process until training is complete. Since training can take a significant amount of time, this method is not recommended.

#### Execution on detached thread (recommended)

``` py linenums="1"
from MAGIST.TaskManagment.ThreadedQueue import MainPriorityQueue # (1)

queue = MainPriorityQueue("config/config.json") # (2)
queue.detach_thread() # (3)

queue.put_queue(cnn, name="MAGIST_CNN_Trainer", priority=10) # (4)

...

queue.join_thread() # (5)
```

1. Import the multicore threading library.
2. Instantiate the class and pass the local path to the config file. This path changes based on your development environment.
3. Daemonize and detach the queue from main thread.
4. Pass the `cnn` class *object* to the queue daemon along with a name and priority. Please not that `cnn` does not have parenthesis because we are passing an object and not the return of the function. The daemon will execute the function when the queue permits. Also note that the highest priority is 1, so setting the CNN lower allows the computer to finish more important tasks like image downloading or audio processing first. 
5. When your code is finished, remember to join the detached thread with the main thread. Otherwise, the detached threads may continue to run in the background. 

This method is recommended because you can continue running more prevalent processes in the foreground while off-loading the CPU-intensive CNN to the background.

!!! warning

     Please not that `cnn` does not have parenthesis because we are passing an object and not the return of the function. The daemon will execute the function when the queue permits.

!!! warning

    When your code is finished, remember to join the detached thread with the main thread using `queue.join_thread()`. Otherwise, the detached threads may continue to run in the background. 

## Advanced
This method has an identical end result except you will manually run individual commands to get fine-grain control over the exact CNN flow. Most of the functions that will be executed here are identical to the `cnn.__call__()` or `cnn()`.  

Here is what `cnn.__call__()` looks like:

``` py linenums="1"
def __call__(self):
    """Calls the train method."""
    self.log.info("Automated Trainer --> Loading data...")
    train, test = self.load_data() # (1)
    self.log.info("Automated Trainer --> Data loaded successfully.")
    self.log.info("Automated Trainer --> Building model...")
    self.compile_model() # (2)
    self.log.info("Automated Trainer --> Model built successfully.")
    self.log.info("Automated Trainer --> Setting up callbacks...")
    self.callbacks_init() # (3)
    self.log.info("Automated Trainer --> Callbacks setup successfully.")
    self.log.info("Automated Trainer --> Training model...")
    self.train() # (4)
    self.log.info("Automated Trainer --> Training completed successfully.")
```

1. The data is being loaded from the configuration file. Ensure that the paths to the dataset are consistent. Also ensure that the data directory is structured as follows: 
```
    - Data
        - class_1
            - img1.jpg
            - img2.jpg
            - ...
        - class_2
            - img1.jpg
            - img2.jpg
            - ...
        - class_3
            - img1.jpg
            - img2.jpg
            - ...
        - ...
```
Also note that MAGIST will automatically perform the train-test split when this function is called as dictated by the configuration file.
2. This function will simply compile the model and prepare it for training.
3. This function will initialize all Tensorflow callback that are setup including Tensorboard and TF Checkpoints.
4. This is the function that will run the actual training procedure. 

!!! warning

    Please ensure that the data directory is structured as follows: 
    ```
    - Data
        - class_1
            - img1.jpg
            - img2.jpg
            - ...
        - class_2
            - img1.jpg
            - img2.jpg
            - ...
        - class_3
            - img1.jpg
            - img2.jpg
            - ...
        - ...
    ```
    Otherwise, the data may load incorrectly or the program might crash altogether.

Since that was a class definition, you must instantiate the class and call the code through the arguments and returns like this:

``` py linenums="1"
from MAGIST.Vision.FullySupervisedModels.MAGIST_Lite_Detector import MAGIST_CNN, MAGIST_CNN_Predictor

cnn = MAGIST_CNN("config/config.json")

train, test = cnn.load_data()
model = cnn.compile_model()
ckpt, manager, checkpoints = cnn.callbacks_init()
cnn.train()
```

You will have to follow a similar process to daemonize it:

``` py linenums="1"
from MAGIST.TaskManagment.ThreadedQueue import MainPriorityQueue 

queue = MainPriorityQueue("config/config.json") 
queue.detach_thread() 

queue.put_queue(cnn.train, name="MAGIST_CNN_Trainer", priority=10) 

...

queue.join_thread() 
```

### Additional Methods
The actual CNN model is not a sequential but a standard TF model defined as a class. To access that class, you must use the hidden class as follows:

``` py linenums="1"
tf_model = _CNN()
```

This class exposes one method (besides `__init__`): `call`. This function defines the forward pass of the model during training and prediction. It will accept the input as an argument and return the final output. `__init__` will inherit the `tf.keras.models.Model` class and initialize the model by building the network.

The `MAGIST_CNN` class also has some hidden functions that run in the background when the higher-level functions are called: `__train_step` and `__train_step`. These have `@tf.function` decorators that will optimize their execution and are meant to step training and run the forward pass. 

The last additional function that `MAGIST_CNN` can provide is the `get_class_names()` method. It will just return an array of classes where the index corresponds with the output of the Tensorflow model itself.

## Next Steps
Now that the model is trained, you can run predictions on the model. The next page will explore the prediction capabilities of MAGIST.