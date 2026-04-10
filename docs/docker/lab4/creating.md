# C. Creating my own image

## Introduction to Dockerfile

To create our own image we need to put instructions in a file called **Dockerfile**.

Our image will be called `simple`.

Create a folder, go into it:

```bash
mkdir simple
cd simple
```

Create an empty Dockerfile

```bash
touch Dockerfile
```

Our simple image will run ROS2 foxy in Ubuntu 20.04 and the
console will be executed as user level instead of root.

## Docker build instructions

The docker instruction starts with a keyword :

<span style="color:purple">FROM</span> <span style="color:blue">image</span> : defines the starting image  
<span style="color:purple">LABEL</span> <span style="color:blue">name=”val”</span> : some meta information (version, maintainer, description, ...)  
<span style="color:purple">ARG</span> <span style="color:blue">name=val</span> : define variable name with value val (can be changed at execution with –build-arg nam=val)  
<span style="color:purple">ENV</span> <span style="color:blue">name=val</span> : define environment variable name with value val  
<span style="color:purple">RUN</span> <span style="color:blue">cmd</span> cmd : execute command cmd  
<span style="color:purple">USER</span> <span style="color:blue">username</span> : change the executing user to username, note
that RUN is executed by default as root  
<span style="color:purple">WORKDIR</span> <span style="color:blue">path</span> : change the path of the working directory  

*From : https://docs.docker.com/engine/reference/builder*

## Write a simple Dockerfile

First we need to specify the base image using `FROM`, then we add following instructions defining how the base image is modified.

Copy the following content in your Dockerfile as a first test to use Ubuntu 20.04 LTS :

```bash
#Download base image ubuntu 20.04
FROM ubuntu:20.04

# LABEL about the custom image
LABEL maintainer="Dupond"
LABEL version="0.1"
LABEL description="This is custom Docker Image for \
student lab purpose"

# Disable Prompt During Packages Installation
ARG DEBIAN_FRONTEND=noninteractive

# Update Ubuntu Software repository
RUN apt update
```

Then we can create and test our image :

Build the image (do not forget the dot . at the end)
```bash
docker build -t simple .
```

Check it exists

```bash
docker images
```

Test if it works
```bash
docker run --rm simple:latest cat /etc/lsb-release
```

## Install ROS2 Foxy on your new image

!!! Note "Issue 1"
    Add commands in your Dockerfile to install ROS2 foxy minimal version (we don't need Desktop tools), so your Dockerfile looks like :

    ```bash
    #Download base image ubuntu 20.04
    FROM ubuntu:20.04

    # LABEL about the custom image
    LABEL maintainer="Dupond"
    LABEL version="0.1"
    LABEL description="This is custom Docker Image for \
    student lab purpose"

    # Update Ubuntu Software repository
    RUN apt update

    # Install ROS2 Foxy minimal version
    # TODO

    # add required packages
    RUN apt install -y ros-foxy-teleop-twist-keyboard
    ```

!!! Tip
    The building process is not interactive, so don't forget to use the `-y` option when necessary, to automatically accept changes.

!!! Tip "Solution 1"

    ```bash
    #Download base image ubuntu 20.04
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
    RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
    -o /usr/share/keyrings/ros-archive-keyring.gpg
    RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
    http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" \
    | tee /etc/apt/sources.list.d/ros2.list > /dev/null
    RUN apt update && apt -y upgrade
    RUN apt install -y ros-foxy-ros-base python3-argcomplete

    # add required packages
    RUN apt install -y ros-foxy-teleop-twist-keyboard
    ```

Build your image, it will automatically replace your previously created `simple` image.

## Test the newly created image

We will now test if ROS2 Foxy is working in the container using **2** terminals connected to the container.  

In the first terminal, run the container with :
``` bash
docker run --rm -it simple:latest /bin/bash
```

do :
``` bash
source /opt/ros/foxy/setup.bash
```

and execute:
``` bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

In a second terminal, get the container ID with `docker ps`  
Connect with the second terminal to the same container with :
``` bash
docker exec -it <Container_ID> /bin/bash
```

!!! Note
    The docker exec command runs a new command in a running container

do :
``` bash
source /opt/ros/foxy/setup.bash
```
and execute :
``` bash
ros2 topic list
```
and
``` bash
ros2 topic echo /cmd_vel
```

Press keys to move the robot in terminal 1 and check that messages are published on the `/cmd_vel` topic in terminal 2.


!!! Note
    Notice you are now using ROS2 Foxy on Ubuntu 20.04 as a lightweight application container from your host machine's OS (probably not Ubuntu 20.04)

## Create a user

Add last commands in Dockerfile to create a user (rosuser) and to initialize ROS2 Foxy

``` bash
    #Download base image ubuntu 20.04
    FROM ubuntu:20.04
    ...
    ...
    ...
    ...

    # define a user
    ENV newuser=rosuser
    RUN useradd -ms /bin/bash ${newuser}
    USER ${newuser}
    WORKDIR /home/${newuser}

    # add ROS2 setup
    RUN /bin/bash -c 'echo "source /opt/ros/foxy/setup.bash" >> /home/${newuser}/.bashrc'
```
If everything goes fine, ROS2 commands can be executed without
doing `source /opt/ros/foxy/setup.bash` and the prompt should be
at user level instead of root level.

## Sharing ROS2 between host and container

In this example a ROS2 topic is published in the container, and it
will be displayed on the host computer.

In a first terminal, start the ROS container in interactive mode :
```bash
docker run --rm -it simple:latest /bin/bash
```

then publish /hi topic at 1 Hz
``` bash
ros2 topic pub -r 1 /hi std_msgs/msg/String "{data: 'Hi'}"
```

Open a second terminal on the host (not in the container)

!!! Note "Issue 2"
    Display the messages published on the /hi topic sent by the container

!!! Tip
    Set `ROS_DOMAIN_ID` to appropriate value and `ROS_AUTOMATIC_DISCOVERY_RANGE` to `LOCALHOST` to avoid conflicts on the network.

!!! Tip "Solution 2"

    ```bash
    ros2 topic list
    ros2 topic echo /hi
    ```