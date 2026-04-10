# Lab 6 - Develop a container application

!!! Warning
    LINUX ONLY

**Goal of the lab** : Learn how to share the host's display with a container

Create the following python script:
``` python
import pyqtgraph as pg
import numpy as np
x = np.random.normal(size=1000)
y = np.random.normal(size=1000)
pg.plot(x, y, pen=None, symbol='o')
pg.exec()
```


**Application** :  
Build an image from Ubuntu 20.04 with python3 and pyqtgraph. Then executes the container with this
image so that python3 automatically launch a python code showing a plot.

Create a container with :

- Ubuntu 20.04
- python3
- python3-pip
- pyqtgraph
- pyside6
- numpy
- Shared folder with the python script

Execute the python script and show the plot at the start of the container.

You should see the container executing normally but nothing appears.

Now, try sharing the host's display with the container.

The host computer must allow the docker container to display graphical application, this permission is given by
```bash
xhost +local:docker
```

Share X11 graphical user interface using `-e DISPLAY -v /tmp/.X11-unix/:/tmp/.X11-unix​`

Now a window should open at the start of the container.

!!! Warning
    On Ubuntu, your display server might be Wayland by default, but this kind of application requires Xorg.  
    You can switch from your user connection interface using the settings gear near "Sign in" before starting the session.