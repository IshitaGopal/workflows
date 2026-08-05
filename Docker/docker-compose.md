# Docker Compose

- https://gurusalih.medium.com/end-to-end-docker-1d16d5a55424

## Why Docker Compose?

1. Applications are composed of many different parts: APIs, databases, other services, microservice applications

2. When creating containerized applications, all these apps must be deployed and run together because they have dependencies on each other. 

3. We have a set of containers running different apps and services in them that need to run together and talk to each other. 

4. Docker Compose allows you to define and run multiple services and apps that belong together in 1 isolated environment. → want to deploy multiple docker containers where each container has its different config options, you can use docker-compose t manage these containers easily. Useful for multi-container Docker applications.

## Without Docker Compose 
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