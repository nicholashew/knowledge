# Redis Installation on Docker

## Prerequisites

To use Docker on your laptop, you need to install Docker Desktop. There is a version for Mac, Windows, and Linux.

## Installation

From the terminal, run:

```
docker pull redis
```

From the terminal, run:

```
docker run --name redis-container -p 6379:6379 -d redis:latest
```

This will create a container with default redis port 6379, you translate the command as:

- `docker run` - Run a command in a new container
- `--name <your-container-name>` - Assign a name to the container to be created, in this example, it will be named as `redis-container`
- `-p 6379:6379` - Publish or expose port (-p, --expose). In this example, this binds port 6379 of the container to TCP port 6379 on 127.0.0.1 (localhost) of the host machine (local machine). You can also specify udp and sctp ports.
- `-d redis:latest` - Run redis container in background and print container ID

Next, run the command below to show all containers, to verify if the container created is running.

```
docker ps -a
```

Example

```
$ docker run --name redis-container -p 6379:6379 -d redis
Unable to find image 'redis:latest' locally
latest: Pulling from library/redis
33847f680f63: Pull complete
26a746039521: Pull complete
18d87da94363: Pull complete
5e118a708802: Pull complete
ecf0dbe7c357: Pull complete
46f280ba52da: Pull complete
Digest: sha256:cd0c68c5479f2db4b9e2c5fbfdb7a8acb77625322dd5b474578515422d3ddb59
Status: Downloaded newer image for redis:latest
eff7dedc53ec235068d543574111055a08d84b518f149ba6aac4adb6e63181d4

docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                       NAMES
eff7dedc53ec   redis     "docker-entrypoint.s…"   34 minutes ago   Up 28 minutes   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   redis-container
```

## Interact with Redis from your terminal

Run redis-cli using this command:

```
docker exec -it redis-container redis-cli
```

## Reference

- [Redis Command-line reference](https://redis.io/docs/manual/cli/)
- [Docker Command-line reference](https://docs.docker.com/engine/reference/commandline/run/)
- [Installing Redis Server Using Docker Container](https://medium.com/idomongodb/installing-redis-server-using-docker-container-453c3cfffbdf)
