---
title: "Docker Containers Explained: Images, Containers, and Volumes"
description: "Understand Docker images, containers, registries, ports, and volumes, with a practical workflow for running an application locally."
slug: "docker-containers-explained"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "DevOps"
tags:
  - "Docker"
  - "Containers"
  - "DevOps"
  - "Deployment"
relatedTools: []
relatedArticles: []
draft: false
---

# Docker Containers Explained: Images, Containers, and Volumes

Developers often face differences between local, test, and production environments. An application may work on one machine but fail on another because of operating-system packages, runtime versions, environment settings, or dependencies.

Docker helps package an application and its required runtime environment into a portable image that can be run as a container.

## What is Docker?

Docker is a platform and ecosystem for building, distributing, and running containerized applications.

A container is an isolated process environment that uses the host operating system's kernel. Unlike a traditional virtual machine, a container does not normally boot a separate guest operating system.

Containers can make application environments more consistent, but they do not automatically make software portable across every CPU architecture or operating system.

## Image versus container

These two terms are related but different:

- **Image:** a read-only template containing application files, runtime components, metadata, and configuration.
- **Container:** a running or stopped instance created from an image.

An image can be used to create many containers. Each container has its own writable layer and runtime state.

## What is a Dockerfile?

A Dockerfile describes how to build an image.

A simple Node.js example:

~~~dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
~~~

This file selects a base image, creates a working directory, installs dependencies, copies source code, documents a port, and defines the default command.

The exact base image and Node.js version should match the application's compatibility and support requirements.

## Build and run an image

Build an image from the current directory:

~~~bash
docker build -t sample-web-app .
~~~

Run a container and map a host port to the container port:

~~~bash
docker run --rm -p 3000:3000 sample-web-app
~~~

The mapping exposes the container's port 3000 on the host's port 3000. The application inside the container must listen on an appropriate interface, commonly 0.0.0.0, rather than only localhost.

## Understand port mapping

Containers have their own network namespace. A process listening inside a container is not automatically reachable from the host or other machines.

The option:

~~~bash
-p 8080:3000
~~~

maps host port 8080 to container port 3000.

This distinction is useful when multiple containers use the same internal port or when the host already has a service running on a particular port.

## What are Docker volumes?

Containers are often treated as replaceable runtime instances. If a container is removed, its writable layer is removed as well.

Volumes provide persistent storage outside a container's writable layer.

For example:

~~~bash
docker volume create app-data

docker run -d   --name database   -v app-data:/var/lib/app   sample-database
~~~

The mount path depends on the software inside the image. Use the documented data directory for the actual application or database.

Volumes are useful for databases, uploaded files, and state that must survive container replacement. They are not a substitute for backups.

## Environment variables

Containerized applications often receive configuration through environment variables:

~~~bash
docker run --rm   -e APP_ENV=development   -p 3000:3000   sample-web-app
~~~

Avoid placing real passwords or API keys directly into shell history, Dockerfiles, or image layers. Use an appropriate secret-management mechanism for sensitive values.

## Useful Docker commands

| Command | Purpose |
|---|---|
| docker images | List local images |
| docker ps | List running containers |
| docker ps -a | List all containers |
| docker logs CONTAINER | View container logs |
| docker exec -it CONTAINER sh | Open a shell in a running container |
| docker stop CONTAINER | Stop a container |
| docker rm CONTAINER | Remove a container |
| docker rmi IMAGE | Remove an image |
| docker inspect CONTAINER | Inspect configuration and runtime details |

Be careful with commands that remove containers, images, or volumes. Confirm the target before deleting persistent data.

## What is Docker Compose?

Docker Compose defines and runs multi-container applications using a YAML configuration file.

A development stack might include:

- A web application.
- An API service.
- A database.
- A cache.

Compose can start services together and configure their networks, volumes, and environment settings.

A Compose file should not contain production secrets in plain text. Use supported secret mechanisms and environment-specific configuration.

## Common Docker mistakes

### Running as root unnecessarily

Use a non-root user inside the container when practical. This can reduce the impact of an application compromise.

### Using the latest tag without control

A floating image tag can change over time. Pin tested versions or digests when reproducibility matters.

### Storing database files only in the container layer

Use persistent volumes and maintain backups.

### Copying secrets into an image

Files added during image builds may remain in image layers. Use build secrets or runtime secret delivery rather than embedding credentials in the image.

### Ignoring image size and vulnerability updates

Choose suitable base images, rebuild regularly, and scan dependencies and images as part of the delivery process.

## Frequently asked questions

### Is a Docker container a virtual machine?

No. Containers share the host kernel and isolate processes and resources. Virtual machines run a separate guest operating system under a hypervisor.

### Does Docker guarantee the same behavior everywhere?

No. CPU architecture, kernel features, mounted files, environment variables, networking, and external services can still differ.

### Can a container store data permanently?

A container's writable layer is not a durable storage strategy. Use volumes or external storage for persistent data.

## Further reading

- [Docker Docs: Get started](https://docs.docker.com/get-started/)
- [Docker Docs: Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Docs: Volumes](https://docs.docker.com/engine/storage/volumes/)

---
