# Docker Basics

**Why I use it**

I'm currently in preparation for the Security+ exam and about to start a job in Consulting for Cybersecurity focused on External Attack Surface Management. 
So I wanted to spin up a DVWA, a damn vulnerable web application where you can try out a lot of different attacks to deepen your understanding about these kind of things. 
But if I would have wanted to install it on my Fedora I would have to install PHP, apache and a lot of other things. 
This can create a lot of clutter in your system it can clash with other programs running on my laptop. Docker solves that. 
It uses containers that are like isolated boxes on my computer. Each container has its own webserver, database. Even if I break something, very likely to happen, I can just restart it and start over. 
Docker is also the standard of how software is being deployed today. it can run on everything independently. So it is not only great for my homelab but also for my skill set in the future.

**How it works**

In Docker there are images. They are like a blueprint on what software to install and how to configure it. A container is the running instance of that image and built after its rules with no limit on how many can be created out of one image.


**Port Mapping**

The container is isolated so my browser on Fedora cannot access it. With port mapping a bridge between a port on my laptop and a port on the container is being built. When the request from my browser gets to port 8080 for example and I assigned that to port 80 on the container, the request gets linked to port 80 in my container. When I go to localhost:8080 on my laptop I land on the DVWA page even though it runs in an isolated box. -p 8080:80 — on the left side is my computer port and on the right is the container port. What has to be considered is that I cannot open a second 8080 port on my computer as it is being used already. I use 9090:80 for example. Port 80 on the container can be used again because each container is its own isolated box with its own network. They don't know about each other.

**Starting Docker & Commands**

```bash
sudo systemctl start docker
```

`sudo` runs the command as admin. `systemctl` controls services on Linux
`start docker` tells it to start. without this running, no container would work
```bash
sudo systemctl enable docker 
```

`sudo` runs the command as admin. `systemctl` controls services on Linux
`enable docker`start automatically every time I boot my laptop. Only need to run this once

```bash
docker run -d -p 8080:80 --name dvwa
```

Creates a new container.
`-d`runs in the background so my temrinal stays free
`-p`




















<!-- ## Key Concepts

**Image**
A blueprint that describes what software to install and how to configure it. Downloaded from Docker Hub. Does not run by itself.

**Container**
A running instance of an image. Created with `docker run`. Multiple containers can be created from the same image, each fully independent.

**Port Mapping** (`-p 8080:80`) 
Connects a port on the host (left) to a port inside the container (right). Without this, the container is isolated and unreachable from outside. Multiple containers can use the same internal port (e.g. 80) because they are isolated — only the host port must be unique.

**Volumes**persistent storage that survives container deletion. Without a volume, all data inside a container is lost when it is removed.

## Container Identification
Every container gets a random ID (e.g. `f43905dfabad...`) and a random name (e.g. `dreamy_mendel`) on creation. Both can be used to reference the container. You don't need the full ID — just enough characters to be unique. If only one container starts with `f43`, that's enough. With `--name dvwa` you assign your own name and never need the ID at all.
## Common Commands

| Command | What it does |
|---------|-------------|
| `docker run -d -p 8080:80 --name dvwa image` | Create and start a container in the background with port mapping and a custom name |
| `docker ps` | Show running containers |
| `docker ps -a` | Show all containers including stopped ones |
| `docker logs [name]` | Show the output/logs of a container |
| `docker stop [name]` | Stop a running container |
| `docker rm [name]` | Delete a stopped container |
| `docker start [name]` | Restart a stopped container (keeps data) |
| 'do -->