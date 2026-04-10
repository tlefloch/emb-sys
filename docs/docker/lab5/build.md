# C. Build a multiplatform image

## Target

Our target computer is a Raspberry Pi with Ubuntu 20.04.

!!! Note "Issue 1"
    Find the target platform

!!! Tip
    `linux/amd64`, `linux/arm64`, `windows/amd64` ?

!!! Tip "Solution 1"
    The target platform is `linux/arm64`
    

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

!!! Tip "Solution 2"
    Dockerfile :
    ``` bash
    FROM ubuntu:20.04

    # LABEL about the custom image
    LABEL maintainer="Dupond"
    LABEL version="0.1"
    LABEL description="This is custom Docker Image for \
    student lab purpose"

    # Update Ubuntu Software repository
    RUN apt update

    # Install ROS2 foxy minimal version
    RUN apt install -y software-properties-common
    RUN add-apt-repository universe
    RUN apt update && apt install -y curl
    RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
    RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
    http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" \
    | tee /etc/apt/sources.list.d/ros2.list > /dev/null
    RUN apt update && apt -y upgrade
    RUN apt install -y ros-foxy-ros-base python3-argcomplete
    RUN apt install -y ros-dev-tools

    # add required packages
    RUN apt install -y ros-foxy-teleop-twist-keyboard


    # define a user
    ENV newuser=rosuser
    RUN useradd -ms /bin/bash ${newuser}
    USER ${newuser}
    WORKDIR /home/${newuser}

    # add ROS2 setup
    RUN /bin/bash -c 'echo "source /opt/ros/foxy/setup.bash" >> /home/${newuser}/.bashrc'
    ```


## Build

Our image is ready to be built.

!!! Note "Issue 3"
    Build the image using `docker build` for two platforms in one single command: your host and our target Raspberry Pi.

!!! Tip
    The command is very similar to a simple platform build.
    Just add the `--platform <platform1>,<platform2>,...` option

!!! Tip "Solution 3"
    ```bash
    docker build --platform linux/amd64,linux/arm64 -t multiplatform .
    ```

!!! Warning
    Building on an emulated architecture is far slower than building on the native architecture.  
    So the multiplatform building process can take a significant amount of time...  
    Time to take a break ?

Now you have a multiplatform image, which can run on multiple platforms !


