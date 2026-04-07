# F. Exercise (work due)

Exercise: **Create a simple Dockerfile to generate an image that executes python2.7 programs in Ubuntu 14.04.**

Objective: **Getting used to the creation of Docker images by defining Dockerfiles**


In this exercise, we will have to create a container using Linux
Ubuntu 14.04 that will execute a python 2.7 code located on the
host.

The first step is to create a folder on the host that will be shared
with the container. In this folder we create a simple Python 2.7
code file, for example test.py with :
``` python
print "oho!"
```

The second step is to create a Dockerfile starting with Ubuntu
14.04, that creates a shared folder in the container and that
installs Python 2.7.

The last step is to create a container that shares a folder with the
host and that executes the Python command.

The Dockerfile and the commands to build and run the
container must be uploaded in an archive (ZIP) file on
MOODLE before 09 april 2026 (23:59).