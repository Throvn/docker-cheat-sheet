---
title: Docker Cheat Sheet
created: '2022-10-21T14:16:58.139Z'
modified: '2023-04-27T08:50:20.167Z'
---

# Docker Cheat Sheet

```shell
docker pull image_name[:tag]
```
Pulls an image from the container registry.

```shell
docker run image_name[:tag]
```
Pulls the image (if necessary) and start a container with the image.

- `-p[host_port]:[container_port]`: 
e.g. `-p6000:3000` binds the containers `3000` port to the hosts `6000` port. So if a webserver is running on port 3000 in the container, it would be reachable on the host via `localhost:6000`
- `--name [container_name]`:
Specifies a name for the container instance. Can be used to reference the container instead of having to work with the container id all the time.
- `-e [KEY]=[VALUE]`:
Passes environment variables in a key value manner down to the container. If you want to have multiple environment variables, specify the `-e` flag multiple times.
- `--net [network_name]`:
Specifies the docker network which the container runs in.


```shell
docker ps [-a]
```
Lists all of the containers **currently running**.
The `-a` shows the entire history of containers.

```shell
docker start image_name [-d]
```
Starts a container of the given image. The `-d` allows for a dispatched running, meaning that the process finishes and runs in the background, rather than taking the entire terminal window for ever.

```shell
docker stop container_id
```
Stops the container with the corresponding id. Stopped containers can be started again, and are visible with `docker ps -a`.

##### Container ports
Multiple containers can open the same ports, there just has to be a different _host port_ binded to the _container ports_.

### Debugging

```shell
docker logs [container_id]
```
Shows you all of the logs if the container is running in detached mode.

##### Container Names
Every container gets a random name assigned to it upon creation. This name can be used to reference the container and is easier to remember. Alternatively, you could als specify a name with the `--name` flag upon creation (`docker run`) of the container.

```shell
docker exec [-it] [container_id] /bin/bash
```
Let's you go into the container as root. 
- `-it [...] [shell_path]`:
Opens up an interactive terminal. Shell path varies from os to os examples are: `/bin/bash` or `/bin/sh`.
> Note: If you want to exit the shell, just type `exit`.

```shell
docker rm [container_id]
```
Removes a container.

```shell
docker rmi [image_id]
```
Deletes a docker image from disk.

##### Developing

###### Docker Network
All of the running containers are in an isolated docker network, which means that the containers can "talk" with each other just by using container names. If you want to talk to the containers from the outside however, you need port binding.

```shell
docker network ls
```
Shows a list of all docker networks.

```shell
docker network create [network_name]
```
Creates a new docker network.

---

## Docker Compose
Compose takes the build configuration you would normally have to do with command line arguments and builds it from the compose yaml file. This will save a lot of potential errors.

**Example:**
```yaml
version: '3'
services:
  [container_name]:
    image: [image_name]
    ports:
      - [host_port]:[container_port]
    environment:
      - [KEY]=[VALUE]
```
> ⚠️ Note: The services in every compose file are already within their own network, so we don't have to worry about creating our own.

```shell
docker-compose up
```
Starts up all of the containers in the compose configuration file. And also puts all of these containers in a new auto created network.
- `-f [file_name].yaml`:
Which file to read the docker compose configuration from (e.g. `mongo.yaml`)

```shell
docker-compose down
```
Stops all of the running containers which are running from the given compose configuration file. And takes down the with it created network.
- `-f [file_name].yaml`:
Which file to read the docker compose configuration from (e.g. `mongo.yaml`)

### Docker files
A `Dockerfile` is a blueprint for creating doker images. 
1) Base your image on another image
```dockerfile
FROM [image_name] # base image
ENV [KEY]=[VALUE] # alternative to compose env
RUN [linux_command]
COPY [source] [destination] # executes on HOST
CMD ["node", "server.js"] # entry point cmd
```
**Note:** Multiple `RUN` commands are possible but only one `CMD` command.

**Note:** The `COPY` commands `[source]` parameter can also take in a relative file path. This path starts in the directory of the `Dockerfile`.

**Note:** `/home/app` is a good directory inside of the container to put your app.

```shell
docker build [image_name]:[tag_name?] [path_of_docker_file]
```
Creates an image from a `Dockerfile`.
- `-t`:
Specify this flag if you use a tag. The tag itself is appended to the `image_name` after a `:`.



