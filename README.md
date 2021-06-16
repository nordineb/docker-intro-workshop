
## Prerequisites

Docker installed
```bash
docker info

❯ docker info
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)
  buildx: Build with BuildKit (Docker Inc., v0.5.1-docker)
  compose: Docker Compose (Docker Inc., 2.0.0-beta.1)
  scan: Docker Scan (Docker Inc., v0.8.0)
```
## Docker images

- A Docker image is an immutable file that contains all files an application needs to run properly. 

- Docker images represent an application and its virtual environment at a specific point in time

- Docker images are just "templated", they cannot start or run. 

- A Docker registry is a storage and distribution system for Docker images. 


```bash
docker pull public.ecr.aws/bacardi/alpine:3.13.0
docker pull docker.io/library/alpine:3.13.0
docker pull alpine:latest

docker image ls 
docker image rm public.ecr.aws/bacardi/alpine:3.13.0
docker image ls 
```

## Running containers

- A container is a runnable instance of an image

```bash
docker run --rm -i -t --name myalpine alpine
```

## Container lifecycles

```bash
docker run -d --name alpine 
docker container ls -a
```

```bash
docker run -it --name testalpine alpine
```

In the container, run:
```bash
echo "hello" > world.txt
cat world.txt
```

Exit the container

check containers 
```bash
docker container ls -a
docker start container testalpine
docker exec -it testalpine /bin/sh
```

Check if you can find the file world.txt
exit the container and delete the container
```bash
docker stop testalpine 
docker rm testalpine
```

### Use `--rm` to automatically remove exited container
```bash
docker run -it --rm -name auto-delete-alpine alpine 
```

### specifying a name
```bash
docker run -it --name alpine1 alpine
docker run -it --name alpine1 alpine
```

## Docker persistent storage

### Bind mounts

Bind mounts are dependent on the directory structure and OS of the host machine

```bash
docker run --rm -it -v /app -v ${PWD}:/myfolder alpine /bin/bash
```

### Docker volumes

volumes are completely managed by Docker.

```bash
docker volume create my-vol
docker volume ls
docker volume inspect my-vol
docker run --mount source=myvol,target=/myvolume alpine /bin/bash
docker volume rm my-vol
```

## Environment variable
```
docker run -it --rm -e NAME=nordine alpine 
```

## Port forwarding

Without port forwarding, we can't reach the container

```bash
docker run -it --rm -d --name hello gcr.io/google-samples/hello-app:1.0 
curl localhost:8080
```

The first parameter is the port on the host and the second is the port on the container

```bash
docker run -it --rm -d -p 33333:8080 gcr.io/google-samples/hello-app:1.0 
curl localhost:33333
```

## Manual Wordpress deployment

### Run mysql in a container

```bash
# create a volume to persist data
docker volume create db_data

# run the container
docker run -d --rm --name mysql -v db_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress mysql:5.7 

#check the logs
docker logs mysql
```

### wordpress

```bash
# create a volume to persist data
docker volume create wordpress_data

# run the container
docker run -d --rm --name wordpress --link mysql:db -p 8000:80 -v wordpress_data:/var/www/html -e WORDPRESS_DB_HOST=db:3306 -e WORDPRESS_DB_USER=wordpress -e WORDPRESS_DB_PASSWORD=wordpress -e WORDPRESS_DB_NAME=wordpress wordpress:latest

#check the logs
docker logs wordpress
```

Open Wordpress
```
open localhost:8000
```

Now delete all containers

## Wordpress deployment with Docker compose

### Starting the deployment

```bash
cd docker-compose
docker-compose up
```

### Tear down

```bash
docker-compose down 
```

## Dockerfile

```bash
cd dockerfile
```

### Build an image and tag it
```bash
docker build -t myalpine .
```

### Create a container from the image
```bash
docker run -it --rm myalpine
```

### Change command line parameters
```bash
docker run -it --rm --entrypoint /bin/sh  myalpine 
```

### Change entrypoint
```bash
docker run -it --rm --entrypoint /bin/sh  myalpine
``` 


## Push images to docker registries

### Run a registry locally
```bash
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

### Retag the previous image
```bash
docker tag myalpine localhost:5000/myremotealpine
```

### Push
```bash
docker push localhost:5000/myremotealpine
```

### Pull
```
docker pull localhost:5000/myremotealpine
```