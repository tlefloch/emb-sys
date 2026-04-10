# C. Build a multiplatform image

## Target

Our target computer is a Raspberry Pi with Ubuntu 20.04.

!!! Note "Issue 1"
    Find the target platform

!!! Tip
    `linux/amd64`, `linux/arm64`, `windows/amd64` ?
    

## Dockerfile

We will use the same Dockerfile used for the **simple** image in Lab 4.

Create a new folder `multiplatform` next to `simple`

``` bash
mkdir multiplatform
cd multiplatform
```

Copy the Dockerfile

``` bash
cp ../simple/Dockerfile .
```

Currently our Dockerfile is set up to install a minimal version of ROS2 Foxy.  
It is enough to run nodes but compilers aren't installed so we can't build packages. 

!!! Note "Issue 2"
    We need :
    
    - `colcon` to build ROS2 packages
    - a C++ compiler such as `g++`
    - `cmake`

    Install them in the Dockerfile.

!!! Tip
    You can install them all at once with the `ros-dev-tools` apt repository.  
    See **Development Tools** in the installation tutorial : [https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html](https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html)



## Build

Our image is ready to be built.

!!! Note "Issue 3"
    Build the image using `docker build` for two platforms in one single command: your host and our target Raspberry Pi.

!!! Tip
    The command is very similar to a simple platform build.
    Just add the `--platform <platform1>,<platform2>,...` option

!!! Warning
    Building on an emulated architecture is far slower than building on the native architecture.  
    So the multiplatform building process can take a significant amount of time...  
    Time to take a break ?

Now you have a multiplatform image, which can run on multiple platforms !


