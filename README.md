# Docker JupyterHub

This setup of [JupyterHub](https://github.com/jupyter/jupyterhub) provides a
[Docker](https://docs.docker.com)-based reference deployment,
it is a customization for the use-cases of planetary data scientists
in the context of the [Europlanet GMAP project](https://europlanet-gmap.eu).
The work is based on Jupyter's
[jupyterhub-deploy-docker](https://github.com/jupyter/jupyterhub-deploy-docker).

This deployment include users authentication through Gitlab/Github systems,
multiple choices of Jupyter Lab environments through Docker images,
multiple storage spaces for private and shared data,
and user initialization script/hooks for live, pre-login environment management.

The Notebook images we use by default here are those provided by GMAP through
DockerHub at:

- [hub.docker.com/u/gmap](https://hub.docker.com/u/gmap)
    - `jupyter-isis-asp`
    - `jupyter-isis`
    - `jupyter-gispy`

    These images are meant to be used in a JupyterHub setup like this
    as well as *standalone*, autonomously, without a JupyterHub server.
    Their source code is provided at GMAP GitHub organization at:
    - [github.com/europlanet-gmap/docker-isis](https://github.com/europlanet-gmap/docker-isis)

In the [`docs`](docs/README.md) you'll find detailed information on how to
change the list of images in your deployment, any image base on the official
[Jupyter Docker Stack images](https://jupyter-docker-stacks.readthedocs.io)
should just work.

## Prerequisites

### Docker

This deployment uses Docker, via [Docker Compose](https://docs.docker.com/compose/), for all the things.

## Run the default setup

To run the JupyterHub with the default Notebook images:

1. [Pull Notebook images](#pull-notebook-images)
2. [Run JupyterHub](#run-jupyterhub)
    - access the service in your browser
    - shutdown the service when done
  

> Per default, a `./tmp` directory will be created in your current working directory to provide
> the necessary host-container directories structure for it to run properly.
> Those directories can be properly defined in `env.notebook`.
>
> - For details on adjusting the settings, see [`docs/README.md`](docs/README.md).

### Pull Notebook images

The Notebook (*aka*, singleuser) images available to the users should be
available in disk beforehand.
A convenience script, [`setup.sh`](setup.sh), is provided to simplify setting up
the system.

To pull the images defined here by default, just do:
```bash
# cd docker/
./setup.sh --pull-notebook-images
```

## Run JupyterHub

Run the JupyterHub container on the host.

To run the JupyterHub container in detached mode:

```bash
docker-compose up -d
```

Once the container is running, you should be able to access the JupyterHub console at

```
http://localhost:8000
```

To bring down the JupyterHub container:

```bash
docker-compose down
```

## Build JupyterHub/Notebook images

I invite you to read the details about building images (and other customizations)
in [`docs/README.md`](docs/README.md).
The short version is as follows.

> Suppose you want to quickly deploy the service using the following images:
> - `gmap/jupyter-isis:8.0.0`
> - `jupyter/scipy-notebook`
>
> You *want* to guarantee all the images have the same `JUPYTERHUB_VERSION`
> installed, this will guarantee hub-notebook fully compatible to each other.

1. Use [docker-compose](https://docs.docker.com/compose/reference/) to build
   the JupyterHub Docker image:

   ```bash
   docker-compose build
   ```

2. Create the list of image to be used (`imagelist`):

   ```bash
   echo "gmap/jupyter-isis:8.0.0" > imagelist
   echo "jupyter/scipy-notebook" >> imagelist
   ```

3. Build the Notebook images from given `./imagelist`:
   ```bash
   ./setup.sh --build-notebook-images
   ```

Now, simply run the service as described in ["Run JupyterHub"](#run-jupyterhub).

---
/.\
