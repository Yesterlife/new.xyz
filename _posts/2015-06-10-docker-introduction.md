---
layout: post
title: Docker Introduction
---

### Introduction

Docker is a tool for packaging and shipping apps. Based on the idea of a shipping container, it provides a standardized way for developers or system administrators to create
lightweight images, or collections of images, for each element of an application, and then easily and quickly deploy the image. Since the image is standardized, it can be 
uniformly deployed on development or production, leading to a much simpler workflow, faster development time for the dev team, and lower management overhead for ops team.

First, a quick overview of a few things Docker is:
*  An open source tool that places a layer on top of Linux container (cgroups and namespaces) to make it simple to package and ship complex apps.
*  A tool for creating a layered filesystem; each layer is versioned and can be shared across running instances, making for much more lightweight deployments.
*  A company behind the project, as well as a site called the "Docker Hub" for sharing containers.

And a few things Docker isn't:

*  A virtual machine. Unlike a true VM, a docker container does not also require a host OS, meaning it's much slimmer that a real VM
*  An infrastructure automation tool, like Pupper or Chef. Like those other tools, Docker is a major player in the DevOps space, but its focus is around running apps in a container environment, as opposed to representing a machine state.


Some key ideas in Docker you should know:

*  an image is a specific state of a filesystem
*  an image is a composed of layers representing changes in the filesystem at varioud points in time; layers are a bit like the commit history of a git repository.
*  a container is a running process that is started based on an image.
*  you can change the state of the filesystem on a container and commit it to create a new image.
*  changes in memory / state are not commited - only changes on the filesystem.

Docker uses the git-style command format:

> `$ docker [OPTIONS] COMMAND [arg...]`

**commands table**

| Command  |  Description
|----------|-----------------------------------------------------------------------|
| attach   | Attach to a running container
| build    | Build an image from a Dockerfile
| commit   | Create a new image from a container's changes
| cp       | Copy files/folders from a container's filesystem to the host path
| diff     | Inspect changes on a container's filesystem
| events   | Get real time events from the server
| export   | Stream the contents of a container as a tar archive
| history  | Show the history of an image
| images   | List images
| import   | Create a new filesystem image from the contents of a tarball
| info     | Display system-wide information
| inspect  | Return low-level information on a container
| kill     | Kill a running container
| load     | Load an image from a tar archive
| login    | Register or log in to the Docker registry server
| logs     | Fetch the logs of a container
| port     | Lookup the public-facing port that is NAT-ed to PRIVATE_PORT
| pause    | Pause all processes within a container
| ps       | List containers
| pull     | Pull an image or a repository from a Docker registry server
| push     | Push an image or a repository to a Docker registry server
| restart  | Restart a running container
| rm       | Remove one or more containers
| rmi      | Remove one or more images
| run      | Run a command in a new container
| save     | Save an image to a tar archive
| search   | Search for an image on the Docker Hub
| start    | Start a stopped container
| stop     | Stop a running container
| tag      | Tag an image into a repository
| top      | Lookup the running processes of a container
| unpause  | Unpause a paused container
| version  | Show the Docker version information
| wait     | Block until a container stops, then print its exit code
