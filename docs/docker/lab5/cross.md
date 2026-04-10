# D. Cross-compile

## Prepare

In your ROS2 workspace, create a folder in which the colcon buil results will be created

```bash
cd <pathtoyourlabfolder>/ros2-ws
mkdir arm64
```

## Run the container

Let's cross-compile our ROS2 package now.

!!! Note "Issue"
    Cross-compile your `cpp_pubsub` package in a Docker container targeting your Raspberry Pi on Ubuntu 20.04. 

    To do so, use `docker run` with the appropriate options:

    - Delete the container after use
    - Select your target platform
    - Share your package's workspace folder with the container's `/home/ros2-ws` folder using a volume
    - Set the working directory inside the container to `/home/ros2-ws/arm64`
    - Run the container with the newly built multiplatform image
    - At the container execution, in a single bash command : `source /opt/ros/foxy/setup.bash`, go to the `arm64` directory, copy the `src` folder here, run `colcon build`.

If the cross-compilation cleanly executed, check your compiled files are, as expected, targeted for `arm64` :

```bash
file arm64/install/cpp_pubsub/lib/cpp_pubsub/talker
```

You should get something like
``` bash
arm64/install/cpp_pubsub/lib/cpp_pubsub/talker: ELF 64-bit LSB pie executable, ARM aarch64, version 1 (GNU/Linux), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=7bf90a3ad33cbe2a990b4474038172b88f9c6588, for GNU/Linux 3.7.0, not stripped
```

It proves the cross-compilation worked.

**Congrats ! Your cross-compiled binaries are ready to be uploaded with `scp` or `rsync` on your arm64 embedded system, and your nodes to be runned !!!**

!!! Note
    Note how you created a completely isolated compilation environnement, which can be use by your coworkers no matter their development environnment.

!!! Note
    As your image also supports amd64, you could also build your package for amd64, and run it in an appropriate container on your host machine.

