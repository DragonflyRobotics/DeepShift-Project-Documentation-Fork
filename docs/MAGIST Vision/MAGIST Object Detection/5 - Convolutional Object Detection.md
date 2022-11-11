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


***More Information Coming Soon!***
