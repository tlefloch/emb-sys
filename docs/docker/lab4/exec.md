# E. Executing a container

## Commands

Several commands can be applied on containers : run, exec, start, stop, rm , ...

- `run` : creating a new container (creating a new container ID in a new entry in
docker container list)
- `exec` : executing an existing container (using the container ID)
- `start` : starting an existing container (using the container ID)
- `stop` : stop a running container (using the container ID)
- `rm` : delete a container (using the container ID)

Notes on using these commands :

- Each run creates a new entry in the container list, it should be used to only
create new containers
- Executing an existing container can be done with start followed by exec
- The `--rm` option can be used with `run` to remove the container at the end of its
execution (so it does not appear in the list of containers)

## An example

Create a new version of the container

``` bash
docker run --name simple -it simple:latest /bin/bash
```

and log out with ctrl-D

Get the container ID :
``` bash
docker ps -a # here 5a166022b7ba
```

Start and execute it

``` bash
docker start 5a166022b7ba
docker exec -it 5a166022b7ba /bin/bash
```

and log out with ctrl-D

The container can be stopped with :

``` bash
docker stop 5a166022b7ba
```

As we do not need it, we can remove this container
``` bash
docker rm 5a166022b7ba
```

Another way is to destroy the container just after the run

``` bash
docker run --rm --name simple -it simple:latest /bin/bash
```

Check if the container has been removed
``` bash
docker ps -a
```