# Image Optimization

## Login to Docker Hub

```bash
docker login -u lakshmi1092
```

---

## Go to the Project Directory

```bash
cd roboshop-docker
```

---

## Build and Push All Images

```bash
for i in $(ls -d */)
do
    cd "$i"
    name=$(basename "$i")
    docker build -t lakshmi1092/$name:v1 .
    docker push lakshmi1092/$name:v1
    cd ..
done
```

### Explanation

- `ls -d */` → Lists all directories in the current location.
- `cd "$i"` → Changes to each application directory.
- `basename "$i"` → Removes the trailing `/` and returns only the directory name.
- `docker build -t lakshmi1092/$name:v1 .` → Builds the Docker image and tags it.
- `docker push lakshmi1092/$name:v1` → Pushes the image to Docker Hub.
- `cd ..` → Returns to the parent directory and continues with the next directory.

> **Note:** `basename` removes the trailing `/` from the directory name and returns only the directory name.

# Stateless vs Stateful Applications

We have three tiers:

- Web Tier
- Application Tier
- Database Tier

---

## Stateless Applications

- Applications where the data is **not important to persist** are called **stateless applications**.
- Even if the container is removed, we can recreate it from the application source code (for example, from Git).
- Since no important data is stored inside the container, volumes are not required.

**Examples:**

- Web Tier
- Application Tier

---


## Stateful Applications

- Applications where the data is **important and must be preserved** are called **stateful applications**.
- If the container is removed, the data should not be lost.
- Therefore, volumes should be created for stateful applications.

**Example:**

- Database Tier

---

## Data Storage

- The data can be stored on the server or any persistent storage location.
- The database stores the actual application data in tables and columns.
- The application reads the data from the database and displays it to the user.
# Docker Named Volumes

## Where is the Data Stored?

Official Docker images usually store their application data in a predefined directory.

**Example:**

- MongoDB → `/data/db`

This is the directory where MongoDB stores its database files.

---

## Docker Volume Commands

List all Docker volumes:

```bash
docker volume ls
```

Create a named volume:

```bash
docker volume create nginx
```

Inspect the volume:

```bash
docker inspect nginx
```

This command shows the details of the volume.

Docker stores the volume data under:

```text
/var/lib/docker/volumes/nginx/_data
```

Docker automatically creates this directory and manages it.

---

## Mount a Named Volume

```bash
docker run -d \
-v nginx:/usr/share/nginx/html \
-p 80:80 \
nginx
```

### Explanation

- `nginx` → Docker named volume.
- `/usr/share/nginx/html` → Directory inside the container where the volume is mounted.

---

## Verify the Volume

Login to the container:

```bash
docker exec -it <container_id> bash
```

Go to the mounted directory:

```bash
cd /usr/share/nginx/html
```

Create a file:

```bash
echo "hello" > hello.txt
```

Exit the container:

```bash
exit
```

Remove the container:

```bash
docker rm -f <container_id>
```

---

## Verify the Data on the Host

Become the root user:

```bash
sudo su -
```

Go to the Docker volume directory:

```bash
cd /var/lib/docker/volumes/nginx/_data
```

The `hello.txt` file is still present because it is stored in the Docker volume.

---

## Start Another Container

```bash
docker run -d \
-v nginx:/usr/share/nginx/html \
-p 80:80 \
nginx
```

This creates a **new container**, but it uses the **same Docker volume** (`nginx`).

Therefore, the new container can access the existing data stored in the volume.

---

## Docker Compose

Remove the application:

```bash
docker compose down
```

> By default, `docker compose down` removes the containers and network, but **named volumes are not removed**.

Start the application again:

```bash
docker compose up -d
```

The containers are recreated and continue using the same named volumes, so the data is preserved.# Include Volumes in Docker Compose

Start the application:

```bash
docker compose up -d
```

Stop and remove the containers:

```bash
docker compose down
```

> **Note:** `docker compose down` removes the containers and network, but **named volumes are not removed** by default. The data is still preserved.

---

## Remove All Images

```bash
docker rmi $(docker images -aq)
```

> This command removes all local Docker images.

---

# Image Optimization

## 1. Use Minimal Images

Update the Dockerfile:

```dockerfile
FROM node:20-alpine3.21
```

Build the image:

```bash
docker build -t lakshmi1092/catalogue:v1 .
```

Push the image:

```bash
docker push lakshmi1092/catalogue:v1
```

> **Note:** Alpine images are smaller than the standard images, which reduces the image size.

---

## Access the Alpine Container

```bash
docker exec -it catalogue sh
```

> Alpine Linux uses `sh` instead of `bash`.

---

## Recreate the Containers

```bash
docker compose up -d
```

Docker recreates the containers using the latest image.

---

## Verify the Image Size

```bash
docker images
```

You can observe that the image size has decreased.

---

# Image Optimization Best Practices

- Use minimal base images (for example, Alpine images).
- Use official Docker images whenever possible.
- Run containers as a non-root user.
