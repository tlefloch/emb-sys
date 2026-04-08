# F. Sharing resources with containers

Containers are, by definition, isolated from the host operating system  
However, it can be useful to share some resources between the host and the container  
A practical example is to allow the container to work in a folder of the host file system

This is done by adding `-v hostpath:containerpath` option at the creation of the container

We will try it

Create a temporary folder:

```bash
mkdir /home/$USER/tmp
```

and a random file
```bash
touch /home/$USER/tmp/blabla.txt
```

!!! Note "Issue"
    Run a container that shares folder `/home/$USER/tmp` on host with folder `/shared` in the container.  
    Check it contains `blabla.txt`.


Exit the container.

Clear the tmp foler

``` bash
rm -r /home/$USER/tmp
```