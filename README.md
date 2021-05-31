# Helper script for running interactive commands in a Docker environment.

This repository contains a bash script `dr` (docker run) that helps running
interactive commands in a Docker environment.

Copy the `dr` script into a directory that is included in the PATH environment variable, e.g /usr/local/bin.

In order to use the `dr` script, a file called `.docker.env` must be present
somewhere between the current directory and the root of the file system.

When the `.docker.env` file is found, it is sourced by the `dr` script in a bash environment.

It is possible to do all kind of processing/verification before the Docker image is run
and eventually call exit 1 on errors.

The `.docker.env` file determines which Docker image to run and if the image is not found
in the local cache it will try to pull it from the given container repository.

The following variables are supported:
* MCHP_DOCKER_NAME    - The name of the container repository (required).
* MCHP_DOCKER_TAG     - The tag (optional). If missing defaults to "latest".
* MCHP_DOCKER_DIGEST  - The digest (optional). If missing, the Docker image is identified as "name:tag".
			If present, the image is identified as "name@digest". 
                        Use digest if it is important to use exactly the same image each time.
* MCHP_DOCKER_OPTIONS - Extra options to the docker run command (optional).

The Docker container is run interactively (-it) and is removed on exit (--rm).
The directory containing the .docker.env file is mounted 1:1 inside the container
and the current directory inside the container will be the same as outside.

Consider a folder structure like this:

    project/
    |-- .docker.env
    |-- bin
    |-- doc
    |-- src
        |-- myscript.sh

If the current directory outside the container is `project/src` and `myscript.sh` is run like this:

    $ dr ./myscript.sh

then the current directory inside the container is also `project/src` and `myscript.sh` has access to
everything inside the `project` folder.

Use the MCHP_DOCKER_OPTIONS if the Docker image has some specific requirements, such as:

* mounting of specific directories
* definition of environment variables
* memory mounted tmpfs

Example of a `.docker.env` file:

    MCHP_DOCKER_NAME=ubuntu
    MCHP_DOCKER_TAG=18.04
    MCHP_DOCKER_DIGEST=sha256:67b730ece0d34429b455c08124ffd444f021b81e06fa2d9cd0adaf0d0b875182
    MCHP_DOCKER_OPTIONS="-v /etc/vim:/etc/vim -e DEBUG=1 --tmpfs /tmp:exec"

This example runs the official Ubuntu 18:04 image from Dockerhub.

Use `dr -h` for runtime help.
