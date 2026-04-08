# D. Pushing a new image

Now we have created our own image, we will publish it online in a repository on Docker Hub.

!!! Note
    The following workflow is similar to GitHub

## Create a repository

First, connect on Docker Hub with your personal account and navigate to your repositories.  
It must be empty.

Now, create one using the `Create a repository` button.  
You must specify a name and its visibility.

Let's call it `lab4-simple`.

## Tag your image

Tags let you manage multiple versions of images within a single DockerHub repository.  
[https://docs.docker.com/docker-hub/repos/manage/hub-images/tags/](https://docs.docker.com/docker-hub/repos/manage/hub-images/tags/)

!!! Note
    If no tag is specified, the image defaults to the `latest` tag

Let's fit our local image with the repository name, and tag it as an `init` version.

```bash
    docker tag simple:latest <your-docker-username>/lab4-simple:init
```

Check your image has been re-tagged

```bash
    docker images
```

!!! Note
    We could have directly set the appropriate tag when building the image

    ``` bash
        docker build -t <your-docker-username>/lab4-simple:init .
    ```

## Push the image

Finally there is just to push the image on the online repository :

```bash
docker push <your-docker-username>/lab4-simple:init
```

Check your image appears on DockerHub.

If your repository is public, anyone can pull your image and use it.