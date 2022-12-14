# MSSQL Installation on Mac OS M1 ARM64

## Prerequisites

To use MSSQL in Mac OS, we will need have a Docker installed in your local machine.

If you don’t already have Docker, download and install it, as follows:

- Download [Docker](https://www.docker.com/products/docker-desktop/) from the Docker official website, make sure you downloading the Mac Apple Chip installation file.
- Once downloaded, open the .dmg file and drag the Docker.app icon to your Applications folder as instructed.
- Launch Docker, you might be prompted for your password.

## Installation 

From the terminal, run:

```
docker pull mcr.microsoft.com/azure-sql-edge
```

This will download the container image to your local machine.

Once the download is completed, run:

```
# mount to specific host folder in local machine 
sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=YourStrongPassword' -p 1434:1433 -v /Users/HappyCoder/workspace/docker-sql-mount-data:/var/opt/mssql/data --name sqledge2 -d mcr.microsoft.com/azure-sql-edge 

# data not persists which is everything reside in the container
sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=YourStrongPassword' -p 1433:1433 --name sqledge -d mcr.microsoft.com/azure-sql-edge
```

> **Note:** Do update the following parameters:
> - `MSSQL_SA_PASSWORD` - the sa password of the database to be created
> - `--name` - the docker container name to be created

Next, check whether the Docker Container is up

```
docker ps
```

You should see the following example if it the container is up

```
$ docker ps

CONTAINER ID   IMAGE                              COMMAND                  CREATED       STATUS       PORTS                              NAMES
d000f354ee5b   mcr.microsoft.com/azure-sql-edge   "/opt/mssql/bin/perm…"   2 hours ago   Up 2 hours   1401/tcp, 0.0.0.0:1433->1433/tcp   sqledge
```

Start the container 

```
docker start sqledge
```

##  Docker CP Command

Reference : https://docs.docker.com/engine/reference/commandline/exec/#options

Copy a local file into container

```
docker cp ./some_file CONTAINER:/work
```

Copy files from container to local path

```
docker cp CONTAINER:/var/logs/ /tmp/app_logs
```

Copy a file from container to stdout. Please note cp command produces a tar stream

```
docker cp CONTAINER:/var/logs/app.log - | tar x -O | grep "ERROR"
```

## Connect to SQL Server

There are 2 ways to connect to SQL
- [mssql-cli command-line query tool for SQL Server](https://learn.microsoft.com/en-us/sql/tools/mssql-cli?view=sql-server-ver16)
- [SQL Server GUI for your Mac – Azure Data Studio](https://learn.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver16)

### MSSQL CLI

- Connect to SQL server 

```
mssql -u sa -p YourStrongPassword
```

- Run a Quick Test to ensure the SQL server is up and running

```
select @@version
```

### Azure Data Studio

For installation guide, you may refer to this [link](https://database.guide/how-to-install-azure-data-studio-on-a-mac/)

## Reference

- https://database.guide/how-to-install-sql-server-on-an-m1-mac-arm64/
- https://www.quackit.com/sql_server/mac/install_sql_server_on_a_mac.cfm
