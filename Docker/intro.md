# Docker
- https://www.youtube.com/watch?v=pg19Z8LL06w
- https://www.youtube.com/watch?v=SXwC9fSwct8


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
- `RUN`: Will execute any command in a shell inside the container environment.
- Next, we need application code →  take files from local folder and copy them into the container.
- `WORKDIR`:
- `CMD`: 

```dockerfile
# Strat from a base image 
FROM abcdef:1.0 

# Copy App code inside the container # Alash at end create if it doesnt exist
COPY xx.json /app/ 
COPY src # Copy whole directory 

# Change into directory ..similar to cd=WORKDIR sets app as default location 
WORKDIR /app 

# Install dependencies 
RUN 

CMD ["", ""]# If its the last command we do CMD 
```

## Build Docker image from above definition 

```zsh
# diiferent ways 
# docker build with-name at-loacation, . = current dir 
docker build -t node-app:1.0 .
docker images # Should show node-app with tag 1.0
# host port: container posrt
docker run -d -p 3000:3000 node-app:1.0
```
