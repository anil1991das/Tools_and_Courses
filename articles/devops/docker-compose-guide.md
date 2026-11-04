---
title: "Docker Compose Explained: Run Multi-Container Applications"
description: "Learn how Docker Compose defines services, networks, volumes, and environment settings for local and multi-container development."
slug: "docker-compose-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "DevOps"
tags:
  - "Docker"
  - "Docker Compose"
  - "Containers"
  - "DevOps"
relatedTools: []
relatedArticles:
  - "docker-containers-explained"
draft: false
---

# Docker Compose Explained: Run Multi-Container Applications

Many applications consist of more than one service. A web application may need an API, a database, a cache, and a background worker. Starting and connecting each container manually can become repetitive.

Docker Compose defines a multi-container application in a YAML file. It lets developers describe services, networks, volumes, and runtime settings in one place.

## What is Docker Compose?

Docker Compose is a tool for defining and running multi-container Docker applications. A Compose project describes the containers that make up an application and how they interact.

A Compose file commonly contains:

- Services: the containers to run.
- Networks: how services communicate.
- Volumes: persistent data storage.
- Environment variables: configuration values.
- Port mappings: connections from host ports to container ports.
- Health checks: checks that help determine service health.

## A simple Compose file

Consider a web application that uses Redis:

~~~yaml
services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      REDIS_HOST: redis
    depends_on:
      - redis

  redis:
    image: redis:7
~~~

Save this as compose.yaml in the project directory.

The web service is built from the local Dockerfile. The redis service uses a Redis image. Compose creates a project network, and services can generally reach one another by service name.

The example is a development starting point. Production deployments need additional decisions about image versions, resource limits, health checks, persistence, and secrets.

## Start and stop the application

Start services in the background:

~~~bash
docker compose up -d
~~~

Show service status:

~~~bash
docker compose ps
~~~

View logs:

~~~bash
docker compose logs -f
~~~

Stop and remove project containers and networks:

~~~bash
docker compose down
~~~

The down command does not normally remove named volumes unless a volume-removal option is explicitly used. Read the command documentation before using destructive options.

## Services communicate using service names

Compose creates a network for the project by default. Containers on that network can discover each other through service names.

In the example, the web application can use redis as the hostname rather than localhost.

Inside a container, localhost refers to that same container. It does not automatically refer to another service or to the host machine.

## Persistent data with volumes

A database container should not keep important data only in its writable container layer.

Example:

~~~yaml
services:
  database:
    image: postgres:17
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
~~~

The named volume survives normal container replacement. This is not a backup. Use a proper backup and recovery strategy for important databases.

Do not commit production passwords into the Compose file or repository. Supply secrets through an approved secret manager or protected environment configuration.

## depends_on and service readiness

depends_on controls service startup order, but basic dependency ordering does not guarantee that a database is ready to accept connections.

Applications should handle temporary connection failures and retry appropriately. Compose health checks and supported dependency conditions can help coordinate readiness in development environments.

## Use environment files

Compose supports environment files for supplying configuration values. A local .env file can make development configuration convenient.

Keep local secret files out of version control. A committed example file should contain placeholders rather than real credentials.

Remember that environment variables are not automatically encrypted. Anyone with sufficient access to inspect the container configuration may be able to retrieve them.

## Build and recreate services

When application code or a Dockerfile changes, use:

~~~bash
docker compose up --build
~~~

Compose can build the image and recreate services as needed.

For reproducible environments, pin image versions and document runtime dependencies. Avoid relying on floating tags without understanding how they may change.

## Useful Compose commands

| Command | Purpose |
|---|---|
| docker compose up | Create and start services |
| docker compose up -d | Start in background |
| docker compose down | Stop and remove containers and networks |
| docker compose ps | Show project containers |
| docker compose logs | Show service logs |
| docker compose exec web sh | Run a command in a running service |
| docker compose build | Build or rebuild images |
| docker compose pull | Pull service images |
| docker compose config | Parse and render the Compose configuration |

## Common mistakes

### Using localhost to connect to another container

Use the Compose service name as the network hostname. localhost points to the current container.

### Assuming depends_on means ready

Startup order is not the same as application readiness. Use health checks and application-level retry behavior.

### Losing database data

Store persistent data in a named volume or managed storage and maintain backups.

### Storing production secrets in YAML

Keep credentials outside source control and restrict access to secret values.

### Using Compose as a full production orchestrator

Compose is useful for local development and some single-host deployments. Large production clusters may require an orchestrator such as Kubernetes or a managed container platform.

## Frequently asked questions

### Is Docker Compose only for development?

No. It can be used in different environments, but production requirements such as high availability, secret distribution, monitoring, and rollout control must be addressed.

### Does Compose create a network automatically?

Compose normally creates a project network unless network configuration changes that behavior.

### Does docker compose down delete named volumes?

Not by default. Explicit options can remove volumes, so check the command before running it when data matters.

## Further reading

- [Docker Docs: Compose](https://docs.docker.com/compose/)
- [Compose file reference](https://docs.docker.com/reference/compose-file/)
- [Docker Docs: Networking](https://docs.docker.com/engine/network/)

---
