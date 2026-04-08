# B. Modifying a docker image

## Running an image from DockerHub

First we pull a linux image from DockerHub.

!!! Note
    Here we take the last stable version of Debian, but other images can be used.

Get linux debian image from DockerHub
``` bash
docker pull debian:stable
```

The debian image should appear locally
``` bash
docker images
```

Then we can execute this image in a container. We want to
interact with the container using a tty terminal.

!!! Note "Issue 1"
    Run the image in a container using `docker run` and access it via a terminal with the correct options to :

    - Make the container interactive  
    - Allocate a pseudo-tty

!!! Tip "Solution 1"

    ``` bash
    docker run -it debian:stable
    ```

!!! Note
    Notice you are now at the root of your image. In fact, `docker run` uses `chroot` which you already know. 

In another terminal, we can verify that the container is running, show running containers :

``` bash
docker ps
```

## Modifying the image

Here we will add some network packages (iproute2 and nmap) to
the basic debian image

First we start the container
``` bash
docker run -it debian:stable /bin/bash
```

!!! Note
    The `docker run` command allows to use a command at run. Here we use `/bin/bash` to open a bash terminal.  
    By default, on the image, the default terminal is already bash, but if we wanted to use `zsh` (of course it needs to be already installed on the image) for example we could use :

    ``` bash
    docker run -it debian:stable /bin/zsh
    ```

Then we try the ip command, it should fail
``` bash
ip addr
```

We install iproute2 and nmap
``` bash
apt update
apt install iproute2 nmap
```

and we test it
```bash
ip addr
nmap -sP 172.12.0.0/24
nmap -sP 172.19.144.0/24
```

We can now quit the container with ctrl-D.

## Updating the image

**If we start again the container the image has not changed and ip
and nmap commands will not work !!!**

If we want our changes to be permanent we need to update the image

First we need to know the container ID used when doing the install of iproute2 and nmap

!!! Note "Issue 2"
    Show all containers and find the container ID

!!! Tip "Solution 2"
    ```bash
    docker ps -a
    ```
    The ID is given in the column `CONTAINER ID`


Let’s say the container ID is `bd5c83e5ce7c` and `debian-network` is the name of the new (modified) image.  
We save our modifications on the image from the container into a new image :
```bash
docker commit bd5c83e5ce7c debian-network
```

Check our new image has been created :
``` bash
docker images
```

We can now test it without the interactive console by adding the command at the end of the line

```bash
docker run debian-network:latest ip addr
```

while the older image should give an error :
``` bash
docker run debian:stable ip addr
```

!!! Note "Issue 3"
    Try using nmap to scan the robotic subnetwork 172.20.25.0/24
    and try to get the date using the container.

!!! Tip "Solution 3"
    ```bash
    docker run debian-network:latest nmap -sP 172.20.25.0/24
    ```

    ```bash
    docker run debian-network:latest date
    ```


## Cleaning

A copy of the container is kept every time we run it. We can see it with :
```bash
docker ps -a
```

We can remove the unwanted save with :
```bash
docker rm ContainerID1 ContainerID2 ...
```

!!! Note "Issue 4"
    If we want to delete the container just after its execution, we can use a certain option of `docker run`  
    Find this option and try it.

!!! Tip "Solution 4"
    Just add `--rm` when we run the container :
    ```bash
    docker run --rm -it debian:stable /bin/bash
    ```