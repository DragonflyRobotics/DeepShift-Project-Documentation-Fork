# Basic Working Principle

Since MAGIST is a multi-agent system, it must use some sort of multiprocessing system to manage all the tasks. Furthermore, it must be able to manage all the needs of the tasks. Since some tasks are independent and can function solely from the config file, others require other inputs or processes to have already been executed. To solve these two issues, MAGIST uses a Threaded Queuing system.

## Threaded Queueing System

This system is composed of two main components: a priority queue and a worker. The priority queue is responsible for managing task priorities and order. When the task is assigned to the queue, the user is required to provide a queue priority. This priority will be used to order tasks. Then the tasks will be assigned from the queue to the workers in sequential order. The worker will remain occupied until the task completes, terminates, or fails. 

## A Closer Look

To further understand the implications and potential complications of this system, consider the following example:

***This example is written in pseudo code for simplicity***

=== "1. Tasks And Priorities"
    The following tasks are to be executed in the order they are listed:
       
        1. TaskA
        2. TaskB
        3. TaskC

        TaskA.priority = 1
        TaskB.priority = 2
        TaskC.priority = 3

        Tasks = [TaskB, TaskA, TaskC]  **Note here that the tasks CAN be randomly arranged since a property of the task is the priority.

=== "2. Setup Queue"
    The tasks are sent to the Queue Handler
        
        Queue(Tasks)

    Here is what the queue recieved:

        TaskA with priority 1
        TaskB with priority 2
        TaskC with priority 3

=== "3. Daemonize"
    Before the queue can run tasks, it must be daemonized and worker threads must be spun off:

        Queue.daemonize(2) **The "2" indicates the number of workers to be initialized. By extension, this is also the number of threads will be spun off (in addition to 1 thread for the queue itself).
            Worker1 - Ready 
            Worker2 - Ready

=== "4. Task Assignment & Execution"
    Now, the queue will recognize the available workers and assign the tasks by priority:
    
    ```
        Worker1 - TaskA - Initalizing
        Worker2 - TaskB - Initalizing
    ```

    ***Note: TaskC is currently on standby and will wait for a worker to become available***

    ... Some time later

    ```
        Worker1 - TaskA - COMPLETE
        Worker2 - TaskB - BUSY
    ```

    ```
        Worker1 - TaskC - Initalizing
        Worker2 - TaskB - BUSY
    ```



