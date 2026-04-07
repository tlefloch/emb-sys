# A. Hello world

## Show existing images and containers

Show containers
``` bash
docker ps
```

Show images
``` bash
docker images
```

!!! Note
    If these commands fail, it means they require to be run as
    root (sudo).  
    To allow the user to execute Docker we need to add it in the docker group and restart the session :

    ``` bash
    sudo usermod -aG docker ${USER}
    ```

    **Now you will have to restart your computer ...**

    After restart, check the previous commands work.

## Run Hello World container

Now we can execute our first container (it just says ”Hello World”
plus some blah-blah)

``` bash
docker run hello-world
```
After the execution, we can check the images and the container
status :

1. Show running containers (nothing should appear)
``` bash
docker ps
```

2. Show all containers (the execution of hello-world container should appear)
``` bash
docker ps -a
```

3. Show images (the locally stored image of hello-world should appear)
``` bash
docker images
```