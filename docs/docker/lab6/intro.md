# Lab 6 - Develop a container application

!!! Warning
    LINUX ONLY

**Goal of the lab** : Learn how to share the host's display with a container


## Check if X11 is installed

Most Ubuntu installations come with X11 pre-installed. To verify, run:

``` bash
echo $XDG_SESSION_TYPE
```

If it returns x11, you’re already using X11.
If it returns wayland, you’ll need to switch to X11.

## Switch from Wayland to X11 (if needed)

First, Log out of your current session.

Then, select Xorg (X11) Session :  
On the login screen, click the gear icon next to the "Sign In" button.  
Select "Ubuntu on Xorg" (or similar, depending on your Ubuntu version).  
Log in as usual

## Application


**Application** :  
Build an image from Ubuntu 20.04 with simple GUI examples. Then executes the container with this
image so that a simple GUI window is automatically opened.

Create an image with :

- Ubuntu 20.04 as the base image
- x11-apps installed

Use `docker run` to run your container and execute the `xeyes` command at the start of the container, as usual, without sharing display.

You should get an error: `Error: Can't open display:`

Indeed, the container is an isolated environnment, so your application can't access your host machine's display. Let's share it.

## Share the display

The host computer must allow the docker container to display graphical application, this permission is given by
```bash
xhost +local:docker
```

Share X11 graphical user interface using `-e DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix​` in `docker run`

Now a window should open at the start of the container.