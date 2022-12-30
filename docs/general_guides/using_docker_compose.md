# How to Use Docker-Compose

## Installing Docker
Use docker.io, which packages the docker engine in the debian/ubuntu philosophy (multi-package):

```
$ sudo apt install docker.io
```

## Installing Docker-Compose:

```
$ sudo curl -o /usr/local/bin/docker-compose -L "https://github.com/docker/compose/releases/download/${DOCKER_VERSION}/docker-compose-$(uname -s)-$(uname -m)"

$ sudo chmod +x /usr/local/bin/docker-compose
```
where `${DOCKER_VERSION}` is the latest release ID.

## Running a Docker-Compose File:
1. CD into the directory with the docker-compose YAML
1. Pull the latest images for the applications defined in the docker-compose file, then launch a new container:

```
$ docker-compose pull
  
  pulling [image]...done

$ docker-compose up -d
```

???+ note docker-compose -d switch
     _The `-d` switch means to run the container in detached mode; important if you want to disconnect from the terminal or issue other commands_

## Updating a Docker-Compose App

1. Pull the latest image and recreate the containers:

```
$ docker-compose pull

  pulling [image]...done

$ docker-compose up --force-recreate -d
```

???+ note Recreating specific containers
     _You can also recreate a specific container by passing its name into the `docker-compose up` command like so:_
     _`docker-compose up --force-recreate -d <container-name>`_

## Checking What Docker Containers Are Running

You can inspect what's running like so:

```
docker ps
```

This will produce the names of the running containers and details about them (including uptime and healthchecks, where applicable).

## Stopping a Running Container
You can stop a container like so:

1. Use `docker ps` to get the ID of the container
1. Stop the container:

```
docker container stop <container-name>
```

## Cleaning Out Old Containers, Volumes, and Images

After pulling a new image, recreating the container, and verifying that the new app image runs well, it's a good idea to wipe the old data from the system.  This will free up TONS of disk space as docker images can be several hundreds of megabytes large:

1. Make sure everything is running before issuing the following command. It is destructive for anything not currently referenced by a running container:

```
docker system prune -af
```
