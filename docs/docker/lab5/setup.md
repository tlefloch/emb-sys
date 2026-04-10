# A. Setup a ROS2 workspace

In your lab's folder, create a ROS2 workspace :

```bash
mkdir -r -p ros2-ws/src
cd ros2-ws/src
```

Then, follow instructions on [https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html#](https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html#) to set up a simple publisher / listener package.  

!!! Note
    Stop before the **Build and run** part, we don't need it.

We will use it as our test package for cross-compilation, no matter what it does.
