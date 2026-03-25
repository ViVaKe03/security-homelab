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
`start docker` tells it to start. without this running, no container would work.

```bash
sudo systemctl enable docker 
```

`sudo` runs the command as admin. `systemctl` controls services on Linux. `enable docker` start automatically every time I boot my laptop. Only need to run this once.

```bash
docker run -d -p 8080:80 --name dvwa vulnerables/web-dvwa
```

Creates a new container.
`-d` runs in the background so my terminal stays free `-p 8080:80` maps my port 8080 to container port 80. `--name dvwa` gives it a name so I can use "dvwa" instead of random IDs. `vulnerables/web-dvwa` is the image from the Docker Hub, Docker downloads it automatically if it's not on my machine yet. This command I used only once to create the container.

```bash
docker stop dvwa
```

Stops the running container. It still exists, it is just not running anymore. Like shutting down a computer without throwing it away. You can still see it with `docker ps -a`.
```bash
docker logs dvwa
```

Shows everything the container has been printing — errors, access logs, startup messages. First thing I check when something is not working
```bash
docker rm dvwa
```

Deletes the container completely. After this `docker ps -a` won't show it anymore and `docker logs dvwa` will give an error because there is nothing left. Important: I have to `docker stop` first, then `docker rm`. A running container cannot be deleted.

After `docker rm` the image is still on my machine. Only the container is gone. If I want DVWA back I need to run `docker run -d -p 8080:80 --name dvwa vulnerables/web-dvwa` again to create a new container from the same image. This new container doesn't have memory from the other and is a brand new one.