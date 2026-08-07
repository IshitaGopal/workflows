# Docker
- https://www.youtube.com/watch?v=pg19Z8LL06w
- https://www.youtube.com/watch?v=SXwC9fSwct8
- https://www.youtube.com/watch?v=zFa9_K8BS8I&t=801s

## Table of Contents
- [When you install Docker Desktop, you get:](#when-you-install-docker-desktop-you-get)
- [Docker Images vs Containers](#docker-images-vs-containers)
- [Docker Registries (e.g., Docker Hub)](#docker-registries-eg-docker-hub)
- [Private Docker Registeries](#private-docker-registeries)
- [Registery vs Repository](#registery-vs-repository)
- [Port Binding: How do we access this container?](#port-binding-how-do-we-access-this-container)
- [Dockerfile: How to create a Docker image](#dockerfile-how-to-create-a-docker-image)
  - [Image Layers](#image-layers)
- [Build Docker image from above definition](#build-docker-image-from-above-definition)
- [Docker Compose](#docker-compose)
  - [Why Docker Compose?](#why-docker-compose)
  - [Without Docker Compose](#without-docker-compose)

## When you install Docker Desktop, you get:
- **Docker Engine** = Docker service. The main part of Docker that enables virtualizations. It is a server that manages images and containers.
- **Docker Client (CLI)**: The ``docker`` command you run in the terminal. Talks with Docker Engine via the REST API.
- **GUI Client**: Also comes with the GUI. These are visual front-ends that also talk to Docker Engine.
- **Docker Registery**: storage + distribution system for Docker images.

## Docker Images vs Containers

- **Image** 
    - A packaged, read-only template that contains the directories, files, application code, dependencies, runtime, system libraries, and configuration needed to run the application. 
    - The image is the artifact that Docker builds.
    - Images do not typically store environment variable values (especially secrets). Those are passed into the container at runtime using .env (`docker run -e ENV_NAME=value myimage`), docker-compose.yml etc.

- **Container** 
    - Image -> Container. This is where the image runs. A running instance of an image = container.
    - We take the package/image -> download it to a local laptop/server -> run it -> runs in a container. 
    - From the same image we can run multiple containers. 
    - Countainers are *Ephemeral* (can be started, stopped, deleted).

```bash
# What images do we have locally?
docker images

# List of running containers
docker ps

# List of all containers running or stopped 
# -a or -all = Lists all containers (stopped and running)
docker ps -a 

```

## Docker Registries (e.g., Docker Hub)

* How do we get Docker images? → From a **Docker registry**.
* A registry is a **storage + distribution system** for Docker images.
* Official/verified images are available.
* Docker Hub (https://hub.docker.com/) is the **biggest registry** and the **default location** Docker checks.
* You don’t need an account to search/pull public images.
* Images are **versioned** using **tags** (e.g., `python:3.11`).
* Special tag: **`latest`** = default/most recent build.
* Best practice: **specify a version tag**, not `latest`.
* !`docker run` command **always creates a new container** unless you restart an existing one.

```bash
# Download image from Docker hub to run locally
# docker pull name:tag
docker pull nginx:1.23

# Run image as a container 
docker run nginx:1.23

# "start worker processes" in the printed logs indicates that the container is running.
# id and name of the container are  assigned.

# Run in detached mode: Runs image as a container in the background without blocking the terminal 
# Use -d or --detach flag = detached mode 
# Runs container in the background and prints the container id 
docker -d run nginx:1.23

# Run commad without pulling..does not have to pull to have locally--can just use run and itll pull for us. Run directly.
docker run nginx:1.22-alpine


# How to see logs now - in detach mode?
docker logs {container-id}
# Like:
docker logs 5d39dfa24f4d

```

## Private Docker Registeries 
- Companies may not want images to be public → private registries.
- AWS ECR = Elastic Container Registeries which provides storage.
- Docker Hub also supports **private registries** → create an account and store private/public images.

## Registery vs Repository
- **Repository**: A collection inside a registry for one application. Each app gets its own **repository**, and inside it you store **multiple image versions/tags**.


## Port Binding: How do we access this container? 
* Containers run inside an isolated Docker network → cannot be accessed from your local machine by default → need **port binding**.
* Each app inside a container runs on a specific **internal port** based on defaults (e.g., 80 for web servers).
* Port binding maps **container port → host port** so you can access it from your machine, e.g.:
  **host 9000 → container 80**.
* Example: `docker run -p 9000:80 nginx`


```bash
# Stop 1 or more running containers
docker stop {container} 
docker stop 5c8facc9cd21

# Port binding 
# Accessible to us at port 9000
# Standard practice to use the same port on the local machine but you can bind to other ports
# host 9000 → container 80
docker run -d -p 9000:80 nginx:1.23

# List of all containers running or stopped 
# -a or -all = Lists all containers (stopped and running)
docker ps -a 

# Restart a container 
docker start {container} #  = Start 1 or more running containers
docker stop 5c8facc9cd21

# Set the name of the container instead of random assignment
# Assign a meanigful container name when we create them
# --name = web-app
docker run --name web-app -d -p 9000:80 nginx:1.23
```

## Dockerfile: How to create a Docker image
- Dockerfile → build → Image → Run → Container.
Say an application is ready  and we want to make it available to users by running it on a server:
- We need to create a **definition** of how to build an image from our application. This is written in a **Dockerfile**
- Dockerfile is a text document that conatines commands to assemble an image.
- Docker can then build an image by reading those instructions.
- Dockerfile **starts from a parent image or "base image"**. Most of these base images are linux based and we build on top of this. These are defined using `FROM`. `FROM` is the 1st **directive** in the Docker file. 
- All **directives are written in caps**. 
- We map everything we would do to run the application locally inside the container -- write any linux command 
- `RUN`: Will execute any command in a shell inside the container environment (e.g. installing dependencies). Each `RUN` creates a new image layer.
- `WORKDIR`: Sets the working directory for any following instructions (`RUN`, `COPY`, `CMD`, ...) — similar to `cd`. Created automatically if it doesn't exist.
- Next, we need application code → take files from local folder and copy them into the container.
- `COPY <src> <dest>`: Copies files/folders from the host into the image. Copying dependency manifests (e.g. `package.json`) before the rest of the code lets Docker cache the install step on rebuilds.
- `CMD`: The default command run when a container starts from this image. Only takes effect once — the last `CMD` in the file wins. Written in exec form: `["executable", "arg1", "arg2"]`.

### Image Layers
- An image is a **stack of layers**, not one solid blob. Most instructions (`FROM`, `RUN`, `COPY`, `ADD`) each add one new layer on top of the previous ones.
- Docker **caches each layer**. On rebuild, if an instruction is unchanged, Docker reuses its cached layer instead of re-running it. The first *changed* instruction forces that layer, and every layer after it, to rebuild.

```dockerfile
FROM python:3.11-slim          # Layer 1: base OS + Python runtime
COPY pyproject.toml uv.lock ./ # Layer 2: adds dependency files
RUN uv sync --frozen           # Layer 3: adds installed packages (.venv)
COPY . .                       # Layer 4: adds the rest of your source
```

- Edit `main.py` and rebuild → layers 1–3 are unchanged, so Docker reuses them from cache. Only layer 4 (`COPY . .`) reruns — `uv sync` is skipped.
- Edit `pyproject.toml`/`uv.lock` and rebuild → layer 2 changes, so layers 2, 3, and 4 all rerun — `uv sync` reinstalls.
- This is why the full example below copies the dependency files and installs them *before* copying the rest of the source: it keeps installs cached across source-only changes.

```dockerfile
# Start from a base image (name:tag)
FROM python:3.11-slim

# Install uv (dependency manager/resolver)
RUN pip install uv

# Change into this directory inside the container — like `cd`.
# Created automatically if it doesn't exist. Every instruction after
# this (COPY, RUN, CMD, ...) runs relative to /app.
WORKDIR /app

# Copy just the dependency files first (better layer caching:
# if only source code changes, the install step below is skipped
# on rebuilds since Docker caches layers by content).
COPY pyproject.toml uv.lock ./

# Install dependencies into .venv. Runs as a shell command inside
# the container. --frozen = don't update the lockfile, just install
# exactly what's pinned.
RUN uv sync --frozen

# Now copy the rest of the application code
# COPY <src on host> <dest in container>
COPY . .

# Document the port the app listens on (doesn't actually publish
# it — that still needs -p at `docker run`)
EXPOSE 8000

# The command that runs when the container starts.
# Only one CMD per Dockerfile — if there are several, the last wins.
# Exec form (JSON array) is preferred over shell form.
CMD ["uv", "run", "main.py"]
```

## Build Docker image from above definition 

- `docker build -t <name>:<tag> <path>` reads the Dockerfile at `<path>` (`.` = current dir, i.e. build context) and produces a new image.
- Rebuilding with the same `name:tag` doesn't create a second image — it replaces what that tag points to (the old image becomes untagged/"dangling" unless something else still references it).

```zsh
# Build: -t names/tags the image, . = build context (current dir)
docker build -t my-app:1.0 .

# Confirm it's there
docker images # Should show my-app with tag 1.0

# Run it as a container — host port : container port
docker run -d -p 8000:8000 --name my-app-container my-app:1.0

# Check it's actually up
docker ps
docker logs my-app-container
curl localhost:8000

# Rebuild after code changes and rerun
docker build -t my-app:1.0 .
docker stop my-app-container && docker rm my-app-container
docker run -d -p 8000:8000 --name my-app-container my-app:1.0
```

## Docker Compose

- https://gurusalih.medium.com/end-to-end-docker-1d16d5a55424

### Why Docker Compose?

1. Applications are composed of many different parts: APIs, databases, other services, microservice applications.
2. When creating containerized applications, all these apps must be deployed and run together because they have dependencies on each other.
3. We have a set of containers running different apps and services in them that need to run together and talk to each other.
4. Docker Compose allows you to define and run multiple services and apps that belong together in 1 isolated environment → want to deploy multiple docker containers where each container has its own config options, you can use docker compose to manage these containers easily. Useful for multi-container Docker applications.

### Without Docker Compose

```bash
# Need Docker installed and running
# 1) Create Docker Network
# where 2 containers run and talk to each other
docker network create mongo-network
docker network ls

# 2) Start MongoDB container
docker run -d \
-p 27017:27017 \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=supersecret \
--network mongo-network \
--name mongodb \
mongo


# 3) Start Mongo Express Container: UI for Mongo
docker run -d \
-p 8081:8081 \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=supersecret \
--network mongo-network \
--name mongo-express \
mongo-express
```

- `docker compose exec` — run a command inside an already-running container (as opposed to `docker compose run`, which spins up a fresh one).
