# Verifing and Check Resource of EC2
## 1. Verifying
- Check OS release:
```bash
	cat /etc/os-release
```
- Check architecture
```bash
	dpkg --print-architecture 
```
	
	Kết quả có thể là amd64 hoặc arm 64
## 2. Check resource
- Check diskspace
```bash
	df -h
```
- Check memory
```bash
	free -h 
```
- Check CPU
```bash
	nproc
```
# Update Ubuntu
- Cập nhật các package
```bash
	sudo apt update
```
- Nâng cấp các package
```bash
	sudo apt upgrade
```
- Cài đặt ca-certificates
```bash
	sudo apt install ca-certificates curl
```
[Docker's official Ubuntu installation instructions use these prerequisites.](https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com)

Absolutely. For an **AWS EC2 instance running Ubuntu**, I recommend installing **Docker Engine from Docker's official APT repository**, not Ubuntu's `docker.io` package. This gives you Docker Engine, Buildx, and the modern Docker Compose plugin, and is the approach Docker documents for Ubuntu. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

I'll walk you through this as if you're setting up an EC2 server for a real application.

## 0. What we're going to build

The end state will look roughly like this:

```text
AWS EC2
└── Ubuntu
    ├── Docker Engine
    │   ├── containerd
    │   ├── Docker CLI
    │   ├── Buildx
    │   └── Docker Compose
    │
    ├── Docker network(s)
    │
    ├── Containers
    │   ├── your application
    │   ├── database
    │   └── nginx/reverse proxy
    │
    └── Persistent Docker volumes
```

We'll also configure:

- Docker to start automatically after reboot
    
- your Ubuntu user to run Docker without `sudo`
    
- Docker Compose
    
- basic security
    
- storage/logging considerations
    
- EC2 networking/security groups
    
- a test container
    
- a sample application deployment
    
- troubleshooting commands
    

---

# 1. SSH into your EC2 instance

From your local computer:

```bash
ssh -i your-key.pem ubuntu@YOUR_EC2_PUBLIC_IP
```

For example:

```bash
ssh -i my-server.pem ubuntu@54.123.45.67
```

If you are using an EC2 instance created from an Ubuntu AMI, the default username is normally:

```text
ubuntu
```

Once connected, confirm you're actually on Ubuntu:

```bash
cat /etc/os-release
```

You should see something similar to:

```text
NAME="Ubuntu"
VERSION="24.04.3 LTS (Noble Numbat)"
```

Also check your architecture:

```bash
dpkg --print-architecture
```

Typical EC2 results are:

```text
amd64
```

or, for Graviton instances:

```text
arm64
```

Docker currently supports both on Ubuntu. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?pStoreID=massmutualn&utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

---

# 2. Check the EC2 resources

Before installing Docker, check disk space:

```bash
df -h
```

And memory:

```bash
free -h
```

And CPU:

```bash
nproc
```

For example:

```text
Filesystem      Size  Used Avail Use%
/dev/root        20G  3.2G   17G  16%
```

Docker images, containers, build cache, and volumes can consume significant disk space.

For a production application, don't blindly assume a tiny root disk will be sufficient.

---

# 3. Update Ubuntu

First:

```bash
sudo apt update
```

Then:

```bash
sudo apt upgrade -y
```

I also recommend:

```bash
sudo apt install -y ca-certificates curl
```

Docker's official Ubuntu installation instructions use these prerequisites. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

---

# 4. Remove conflicting Docker packages

This is important.

Ubuntu may have packages such as:

- `docker.io`
    
- `docker-compose`
    
- `docker-compose-v2`
    
- `docker-doc`
    
- `docker-buildx`
    
- `podman-docker`
    
- `containerd`
    
- `runc`
    

Docker recommends removing conflicting packages before installing Docker Engine from its repository. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?pStoreID=massmutualn&utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

Run:

```bash
sudo apt remove -y \
  docker.io \
  docker-doc \
  docker-compose \
  docker-compose-v2 \
  docker-buildx \
  podman-docker \
  containerd \
  runc
```

If Ubuntu says some packages aren't installed, that's fine.

**Important:** removing these packages does not automatically remove Docker's `/var/lib/docker` data. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?pStoreID=massmutualn&utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

---

# 5. Add Docker's official GPG key

Create the keyring directory:

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Download Docker's official signing key:

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  -o /etc/apt/keyrings/docker.asc
```

Set the appropriate permissions:

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

---

# 6. Add Docker's official APT repository

This is preferable to using Ubuntu's potentially older Docker package.

Run:

```bash
sudo tee /etc/apt/sources.list.d/docker.sources > /dev/null <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

Then:

```bash
sudo apt update
```

Docker's current documentation uses this repository configuration. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

You can verify the repository:

```bash
apt-cache policy docker-ce
```

You should see Docker's repository in the output.

---

# 7. Install Docker Engine

Now install:

```bash
sudo apt install -y \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin
```

These packages give you:

|Package|Purpose|
|---|---|
|`docker-ce`|Docker Engine|
|`docker-ce-cli`|Docker command-line interface|
|`containerd.io`|Container runtime|
|`docker-buildx-plugin`|Modern image builder|
|`docker-compose-plugin`|`docker compose`|

This is the package set recommended by Docker's current Ubuntu instructions. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

---

# 8. Check Docker service

Run:

```bash
sudo systemctl status docker
```

You want to see something similar to:

```text
Active: active (running)
```

If it isn't running:

```bash
sudo systemctl start docker
```

Then:

```bash
sudo systemctl status docker
```

---

# 9. Make sure Docker starts after EC2 reboot

This is important for a server.

Run:

```bash
sudo systemctl enable docker.service
```

And:

```bash
sudo systemctl enable containerd.service
```

Check:

```bash
systemctl is-enabled docker
```

Expected:

```text
enabled
```

Docker documents systemd startup configuration as part of its Linux post-installation procedures. ([Docker Documentation](https://docs.docker.com/engine/install/linux-postinstall?utm_source=chatgpt.com "Linux post-installation steps for Docker Engine | Docker Docs"))

---

# 10. Test Docker

Run:

```bash
sudo docker run hello-world
```

Docker should download the `hello-world` image and print a success message.

Check:

```bash
sudo docker version
```

And:

```bash
sudo docker info
```

Then:

```bash
sudo docker images
```

You should see:

```text
REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
hello-world   latest    ...            ...           ...
```

At this point **Docker Engine is successfully installed**. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

---

# 11. Configure your `ubuntu` user to use Docker

Currently you'll need:

```bash
sudo docker ps
```

every time.

We can allow the `ubuntu` user to run Docker directly.

First check the group:

```bash
getent group docker
```

If it exists, add your user:

```bash
sudo usermod -aG docker ubuntu
```

Or, more generally:

```bash
sudo usermod -aG docker $USER
```

Docker's documentation recommends adding the user to the `docker` group for non-root Docker CLI access. ([Docker Documentation](https://docs.docker.com/engine/install/linux-postinstall?utm_source=chatgpt.com "Linux post-installation steps for Docker Engine | Docker Docs"))

---

# 12. Log out and reconnect

This step is frequently forgotten.

Exit your SSH session:

```bash
exit
```

Then reconnect:

```bash
ssh -i your-key.pem ubuntu@YOUR_EC2_PUBLIC_IP
```

Check:

```bash
groups
```

You should see:

```text
ubuntu docker
```

Now try:

```bash
docker ps
```

You should **not** get a permission error.

Then:

```bash
docker run hello-world
```

---

# 13. Important Docker security warning

There is an important security implication here.

Membership in the `docker` group effectively grants very high privileges on the host. Docker's own documentation warns that the Docker daemon runs with root privileges and access to its socket gives powerful control over the host. ([Docker Documentation](https://docs.docker.com/engine/install/linux-postinstall?utm_source=chatgpt.com "Linux post-installation steps for Docker Engine | Docker Docs"))

So:

```bash
sudo usermod -aG docker ubuntu
```

is convenient, but don't treat Docker group membership as an ordinary low-privilege permission.

For a normal single-user EC2 application server, it's commonly used.

For a highly locked-down environment, consider Docker rootless mode or another architecture.

---

# 14. Verify Docker Compose

Because we installed the Compose plugin, the modern command is:

```bash
docker compose version
```

For example:

```text
Docker Compose version v2.x.x
```

Notice:

```bash
docker compose
```

not:

```bash
docker-compose
```

The former is the current Compose plugin approach.

---

# 15. Understand the difference

You'll encounter these commands constantly:

### Docker

```bash
docker ps
```

Manages individual containers.

### Docker Compose

```bash
docker compose up
```

Manages a multi-container application defined in `compose.yaml` / `docker-compose.yml`.

For example:

```text
My application
│
├── web
├── api
├── postgres
└── redis
```

Compose can manage all of them together.

---

# 16. Create your first real container

Let's run Nginx.

```bash
docker run -d \
  --name nginx-test \
  -p 8080:80 \
  nginx:alpine
```

Check it:

```bash
docker ps
```

You should see something like:

```text
CONTAINER ID   IMAGE          STATUS          PORTS
xxxxxx         nginx:alpine  Up 10 seconds   0.0.0.0:8080->80/tcp
```

The important part is:

```text
8080:80
```

It means:

```text
EC2 port 8080
        ↓
container port 80
```

---

# 17. Test Nginx from the EC2 server

Run:

```bash
curl http://localhost:8080
```

You should get HTML from Nginx.

You can also check:

```bash
docker logs nginx-test
```

---

# 18. Configure the AWS Security Group

This is where EC2 differs from a normal Linux server.

Suppose your Docker container exposes:

```text
8080
```

Docker is listening on:

```text
0.0.0.0:8080
```

But AWS still has its **Security Group** in front of the instance.

You need an inbound rule allowing traffic to that port if you want the Internet to reach it.

For example:

```text
Type        Protocol    Port
HTTP        TCP         80
HTTPS       TCP         443
SSH         TCP         22
```

For SSH, ideally restrict the source to:

```text
YOUR_PUBLIC_IP/32
```

rather than:

```text
0.0.0.0/0
```

Do **not** open SSH to the entire Internet unless you have a specific reason.

---

# 19. Don't expose application ports unnecessarily

A common beginner setup is:

```bash
docker run -p 3000:3000 my-app
```

and then opening:

```text
TCP 3000 -> 0.0.0.0/0
```

That works, but it's usually not how I'd structure a production web server.

A better architecture is:

```text
Internet
   │
   ▼
AWS Security Group
   │
   ├── 80
   └── 443
        │
        ▼
     Nginx
        │
        ▼
   Docker network
        │
        ▼
   Application
```

Then your application doesn't need to be directly exposed to the Internet.

For example:

```text
Internet
   │
   │ HTTPS :443
   ▼
Nginx container
   │
   │ internal Docker network
   ▼
API container :3000
```

---

# 20. Docker ports vs AWS Security Groups

This distinction is extremely important.

Suppose you run:

```bash
docker run -d -p 8080:80 nginx
```

Docker creates:

```text
EC2:8080 → Container:80
```

But AWS Security Groups determine whether external traffic can reach EC2:8080.

So you need **both**:

```text
Internet
   │
   ▼
AWS Security Group
   │
   │ allows TCP 8080
   ▼
EC2
   │
   │ Docker publishes 8080
   ▼
Container:80
```

---

# 21. Be careful with UFW on Docker hosts

This is a particularly important security issue.

Docker's documentation warns that published Docker ports can bypass normal UFW/firewalld expectations. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?pStoreID=massmutualn&utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

So don't assume:

```bash
sudo ufw deny 8080
```

necessarily gives you the protection you expect from Docker-published ports.

On EC2, your **AWS Security Group** should be part of your primary network access-control design.

For more sophisticated firewall rules, Docker recommends working with the `DOCKER-USER` chain rather than assuming ordinary firewall rules will behave as expected. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?pStoreID=massmutualn&utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

---

# 22. Stop the test container

Once you've verified it:

```bash
docker stop nginx-test
```

Remove it:

```bash
docker rm nginx-test
```

You can see stopped containers with:

```bash
docker ps -a
```

---

# 23. Learn the essential Docker commands

These are the commands you'll use constantly.

### List running containers

```bash
docker ps
```

### List all containers

```bash
docker ps -a
```

### Start a container

```bash
docker start CONTAINER
```

### Stop a container

```bash
docker stop CONTAINER
```

### Restart

```bash
docker restart CONTAINER
```

### Remove

```bash
docker rm CONTAINER
```

### List images

```bash
docker images
```

### Download an image

```bash
docker pull nginx:alpine
```

### Delete an image

```bash
docker rmi IMAGE
```

### View logs

```bash
docker logs CONTAINER
```

### Follow logs

```bash
docker logs -f CONTAINER
```

### Execute a command inside container

```bash
docker exec -it CONTAINER bash
```

For Alpine:

```bash
docker exec -it CONTAINER sh
```

---

# 24. Understand Docker storage

Docker stores its data primarily under:

```text
/var/lib/docker
```

Check:

```bash
sudo du -sh /var/lib/docker
```

Docker volumes are important for databases.

For example, don't casually run PostgreSQL like:

```bash
docker run postgres
```

and assume your database data is safely managed.

Instead, use a persistent volume:

```bash
docker volume create postgres-data
```

Then:

```bash
docker run -d \
  --name postgres \
  -v postgres-data:/var/lib/postgresql/data \
  postgres
```

Now the database data lives in a Docker volume rather than only inside the container's writable layer.

---

# 25. Understand the container lifecycle

This is one of the most important Docker concepts.

A container is **not** a VM.

For example:

```text
Docker image
     │
     ▼
Container
     │
     ├── process
     ├── filesystem layer
     └── network
```

If the container dies, you can create another container from the same image.

Persistent data should generally live in:

```text
Docker volume
```

or:

```text
external database/storage
```

rather than relying on the container itself.

---

# 26. Use Docker Compose for real applications

Suppose you have:

```text
API
PostgreSQL
Redis
```

Instead of manually doing:

```bash
docker run ...
docker run ...
docker run ...
```

create:

```text
compose.yaml
```

Example:

```yaml
services:
  api:
    image: my-api:latest
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgres://postgres:password@postgres:5432/mydb
    depends_on:
      - postgres

  postgres:
    image: postgres:18
    restart: unless-stopped
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```

Then:

```bash
docker compose up -d
```

Check:

```bash
docker compose ps
```

Logs:

```bash
docker compose logs
```

Follow API logs:

```bash
docker compose logs -f api
```

Stop:

```bash
docker compose down
```

Start again:

```bash
docker compose up -d
```

---

# 27. Don't put production passwords directly in Compose

For learning, this is okay:

```yaml
POSTGRES_PASSWORD: password
```

For production, don't do this.

Instead consider:

- AWS Secrets Manager
    
- AWS Systems Manager Parameter Store
    
- Docker secrets where appropriate
    
- environment files with carefully controlled permissions
    
- a managed database such as Amazon RDS
    

For an EC2 production environment, I would generally avoid putting important credentials directly into a Git repository.

---

# 28. Configure container restart policies

For production services, you normally want containers to come back after a crash or server reboot.

For example:

```yaml
restart: unless-stopped
```

or:

```yaml
restart: always
```

I generally prefer:

```yaml
restart: unless-stopped
```

for many application deployments.

---

# 29. Monitor Docker disk usage

Run:

```bash
docker system df
```

This shows Docker's disk consumption.

You can inspect:

```bash
docker images
```

```bash
docker ps -a
```

```bash
docker volume ls
```

```bash
docker network ls
```

Be careful with:

```bash
docker system prune
```

It can delete unused Docker resources.

Don't blindly run:

```bash
docker system prune -a --volumes
```

on a production server.

You can accidentally remove data you intended to keep.

---

# 30. Check Docker logs

If an application isn't working:

```bash
docker ps
```

Then:

```bash
docker logs CONTAINER_NAME
```

For example:

```bash
docker logs my-api
```

Follow them:

```bash
docker logs -f my-api
```

With Compose:

```bash
docker compose logs -f
```

Or one service:

```bash
docker compose logs -f api
```

---

# 31. Check Docker's service logs

If Docker itself is having problems:

```bash
sudo journalctl -u docker
```

Follow:

```bash
sudo journalctl -u docker -f
```

Check containerd:

```bash
sudo journalctl -u containerd
```

Check service:

```bash
sudo systemctl status docker
```

Restart:

```bash
sudo systemctl restart docker
```

---

# 32. Test reboot persistence

Once you've configured your actual application, you should test:

```bash
sudo reboot
```

Your SSH connection will terminate.

Wait a little and reconnect.

Then:

```bash
docker ps
```

Check:

```bash
systemctl status docker
```

Your containers configured with an appropriate restart policy should come back automatically.

---

# 33. EC2 architecture I'd recommend

For a typical small production web application:

```text
                     Internet
                         │
                         ▼
                ┌─────────────────┐
                │  AWS Security   │
                │     Group       │
                └────────┬────────┘
                         │
                  80 / 443 only
                         │
                         ▼
                ┌─────────────────┐
                │      EC2        │
                │     Ubuntu      │
                │                 │
                │    Docker       │
                │       │         │
                │       ▼         │
                │     Nginx       │
                │       │         │
                │       ▼         │
                │   Docker net    │
                │       │         │
                │       ▼         │
                │      API        │
                └───────┬─────────┘
                        │
                        ▼
                 RDS / external DB
```

I'd generally avoid exposing PostgreSQL:

```text
5432 → Internet
```

or Redis:

```text
6379 → Internet
```

Those services should normally be private.

---

# 34. EC2 Security Group baseline

For a normal web server:

|Port|Source|Purpose|
|---|---|---|
|22|Your IP only|SSH|
|80|`0.0.0.0/0`|HTTP|
|443|`0.0.0.0/0`|HTTPS|

Avoid:

|Port|Reason|
|---|---|
|2375|Never casually expose Docker's unauthenticated TCP API|
|2376|Docker remote API requires careful TLS/security configuration|
|5432|Don't expose PostgreSQL publicly|
|6379|Don't expose Redis publicly|
|3000|Usually keep application behind reverse proxy|
|8080|Usually keep application behind reverse proxy|

---

# 35. One more important EC2 consideration: architecture

If your EC2 instance is:

```text
t4g
m7g
c7g
```

etc., it is likely **ARM64/Graviton**.

Check:

```bash
uname -m
```

If you get:

```text
aarch64
```

you're on ARM64.

If:

```text
x86_64
```

you're on AMD64/x86.

Docker itself supports both, but **your application images must also support the architecture**. Docker's Ubuntu documentation lists both amd64 and arm64 among supported architectures. ([Docker Documentation](https://docs.docker.com/engine/install/ubuntu/?pStoreID=massmutualn&utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs"))

For example, an image might support:

```text
linux/amd64
linux/arm64
```

or only one of them.

---

# 36. Useful final verification

Run this whole set:

```bash
docker --version
```

```bash
docker compose version
```

```bash
docker version
```

```bash
docker info
```

```bash
docker ps
```

```bash
systemctl is-enabled docker
```

```bash
systemctl is-active docker
```

And:

```bash
docker run --rm hello-world
```

If all of those work, your Docker installation is in good shape.

---

## 37. Official documentation

For reference, Docker's current official Ubuntu installation guide is here:

[Docker Engine — Install on Ubuntu](https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com)

And the Linux post-installation guide:

[Docker Linux post-installation steps](https://docs.docker.com/engine/install/linux-postinstall?utm_source=chatgpt.com)

---

## What I recommend we do next

If this EC2 is for **deploying your actual application**, don't stop at installing Docker. The next setup should be:

```text
EC2 Ubuntu
   ↓
Docker
   ↓
Docker Compose
   ↓
Application container
   ↓
Nginx / reverse proxy
   ↓
HTTPS / Let's Encrypt
   ↓
Domain name
   ↓
Automatic container restart
   ↓
Persistent volumes
   ↓
Logs + monitoring
   ↓
Backups
```

If you tell me **what application you're deploying** (for example **Node.js, Python/FastAPI, Django, Laravel, Java/Spring, React + API, etc.**) and whether you have a **domain name**, I can walk you through the complete EC2 → Docker → Compose → Nginx → HTTPS production deployment step by step.