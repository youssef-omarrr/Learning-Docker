
## Client vs Host vs Registry

![[assets/Pasted image 20250711233649.png]]
### 🧭 1. **Docker Client**

**What it is**:  
The primary way you interact with Docker. It's the command-line tool you use (e.g., `docker build`, `docker run`, `docker push`).

**Role**:

- Sends commands to the Docker Daemon (host) via REST API (usually over a Unix socket or TCP).
    
- Does not do any heavy lifting (like running containers or images).
    

**Example**:  
When you type `docker pull nginx`, the Docker Client sends a request to the Docker Daemon to pull the image from a registry.

---

### 🖥 2. **Docker Host**

**What it is**:  
The system that runs the **Docker Daemon** (`dockerd`). It handles images, containers, networks, volumes, etc.

**Role**:

- Builds, runs, and manages Docker containers and images.
    
- Can be local (your own machine) or remote (e.g., a cloud VM).
    
- The daemon listens for API requests from the Docker Client.
    

**Example**:  
The Docker Daemon on the host system receives the `docker run` request from the client and actually launches the container.

---

### 📦 3. **Docker Registry**

**What it is**:  
A service for storing and distributing Docker images. The default public registry is [Docker Hub](https://hub.docker.com/), but you can also use private registries.

**Role**:

- Stores Docker images (as versions/tags).
    
- Provides endpoints for pushing and pulling images.
    

**Example**:  
When you push an image with `docker push yourname/app`, it uploads the image layers to the registry so they can be pulled later on another machine.

---

### Summary Table:

|Component|Role|Example Use|
|---|---|---|
|Docker Client|Sends commands to the daemon|`docker build`, `docker run`|
|Docker Host|Runs Docker Daemon and containers/images|Executes build and run actions|
|Docker Registry|Stores and serves Docker images|Docker Hub, GitHub Container Registry|

---
## 🐋 Create the `Dockerfile`

A `Dockerfile` is a text file that tells Docker **how to build an image** step-by-step.

Here’s a beginner-friendly version with comments:

```Dockerfile
# Step 1: Use an official Python image as the base
FROM python:3.8

# Step 2: Set environment variables (accessible inside the container)
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Step 3: Set the working directory inside the container
WORKDIR /usr/src/app

# Step 4: Optionally create a mount point for persistent or shared data
VOLUME ["/usr/src/app/data"]

# Step 5: Copy all files from your local directory into the container
COPY . .

# Step 6: Install the required Python packages (Flask in this case)
RUN pip install --no-cache-dir -r requirements.txt

# Step 7: Add metadata labels
LABEL maintainer="your.email@example.com"
LABEL version="1.0"
LABEL description="A Flask application container"

# Step 8: Expose port 5000 to the outside world (Flask default)
EXPOSE 5000

# Step 9: Set default environment variables for app behavior
ENV FLASK_ENV=development

# Step 10: Set an entry point if needed for pre-run logic
# ENTRYPOINT ["python", "init_script.py"]

# Step 11: Define the command to run your app 
CMD ["python", "./app.py"]
```

### `CMD` vs `ENTRYPOINT`:
- `CMD` is more flexible and can be overwritten.

```Dockerfile
# create a user with permissions to run the app
# -S -> create a system user
# -G -> add the user to a group
# This is done to avoid running the app as root
# If the app is run as root, any vulnerability in the app can be exploited to gain access to the host system
# It's a good practice to run the app as a non-root user
RUN addgroup app && adduser -S -G app app

# set the user to run the app
USER app
```
---
## Port mapping
**Port mapping** in Docker connects a port on the **host machine** to a port in the **container**, allowing external access to services running inside.

**Example**:

```bash
docker run -p 8080:5000 myapp
```

This maps port `8080` on your **host** to port `5000` inside the **container** (where the app runs).

---

## Removing and killing containers

- Remove one or more stopped containers (by force):
    ```bash
    docker rm <CONTAINER_ID> --force
    ```
    
- To kill the vmmem process run this as admin in cmd:
```
taskkill /f /im wslservice.exe
```

---
## Mounting app dir to host dir

The `-v "$(pwd):/app"` option in `docker run` mounts your **current host directory** into the **`/app`** directory inside the container, allowing ***live*** file sharing between the host and container.

### Example:

```bash
docker run -v "$(pwd):/app" myapp
```

### Breakdown:

- `-v`: Stands for "volume"
- `"$(pwd)"`: Expands to the current directory on the host
- `:/app`: Mounts it to `/app` inside the container

This is useful for development, so changes on your machine are instantly reflected inside the container.

---

```bash
docker run -v "$(pwd):/app" -v venv_data:/app/.venv myapp
```
###  Explanation:

- `-v "$(pwd):/app"`: Mounts the current working directory into `/app` in the container.
- `-v venv_data:/app/.venv"`: Creates and mounts a **named volume** called `venv_data` to store the virtual environment persistently, independent of your source code.

---
## `docker tag`

The `docker tag` command gives an existing image a new name (and optionally a new version or repository), often used before pushing to a registry.

### Example:

```bash
docker login
docker tag myapp:latest myusername/myapp:v1

```

This tags the local image `myapp:latest` as `myusername/myapp:v1`, ready for pushing to a Docker registry like Docker Hub.

---
The `docker push` command uploads a tagged local image to a Docker registry so it can be shared or deployed on other systems.

### Example:

```bash
docker push myusername/myapp:v1
```

This pushes the `myusername/myapp:v1` image to Docker Hub (or another configured registry). Make sure you're logged in (`docker login`) and that the image is properly tagged.

## Docker compose
**Docker Compose** is a tool that lets you define and run multi-container Docker applications using a `.yaml` (or `.yml`) file, typically named `docker-compose.yml`.

Or you can Just run:
```
docker init
```

---

###  **What the `.yaml` file does**:

It describes the **services**, **networks**, **volumes**, and **build configurations** for your app in a declarative format.

---

### **Example: `docker-compose.yml`**

```yaml
version: "3.9"

services:
  web:
    build: .
    ports:
      - "8080:5000"
    volumes:
      - .:/app
      - venv_data:/app/.venv
    environment:
      - FLASK_ENV=development

  redis:
    image: redis:alpine
```

---

###  **How to use it**

```bash
docker compose up
```

This command reads `docker-compose.yml`, builds images (if needed), and runs all defined services.

---

## Docker compose watch

![[assets/Pasted image 20250712002303.png]]

**`docker compose watch`** is a newer command (introduced in Docker Compose v2.24+) that watches your local files for changes and **automatically restarts containers** or **rebuilds services** when relevant files are updated—ideal for development.

---

###  What It Does:

- Detects changes in source code or configuration files.
- Rebuilds or restarts containers as needed (based on your `docker-compose.yml`).


---

###  Example Usage:

```bash
docker compose watch
```

This command works if your `docker-compose.yml` is already set up with mounted volumes or build contexts.

---

###  Example `docker-compose.yml` for use with `watch`:

```yaml
version: "3.9"

services:
  web:
    build: .
    command: python app.py
    volumes:
	 - .:/app                # Mounts the project directory
     - venv_data:/app/.venv  # Overrides .venv to avoid sharing host's .venv
    ports:
      - "8080:5000"
```

---

### Notes:

- Works best for **development** workflows.
- Requires Docker Compose V2 (not the legacy `docker-compose`).
- No need to manually stop/restart containers when editing files.
    
---

## Full `compose.yaml` file template

```yaml
# specify the version of docker-compose
version: "3.8"

# define the services/containers to be run
services:
  # define the frontend service
  # we can use any name for the service. A standard naming convention is to use "web" for the frontend
  web:
    # we use depends_on to specify that service depends on another service
    # in this case, we specify that the web depends on the api service
    # this means that the api service will be started before the web service
    depends_on: 
      - api
    # specify the build context for the web service
    # this is the directory where the Dockerfile for the web service is located
    build: ./frontend
    # specify the ports to expose for the web service
    # the first number is the port on the host machine
    # the second number is the port inside the container
    ports:
      - 5173:5173
    # specify the environment variables for the web service
    # these environment variables will be available inside the container
    environment:
      VITE_API_URL: http://localhost:8000

    # this is for docker compose watch mode
    # anything mentioned under develop will be watched for changes by docker compose watch and it will perform the action mentioned
    develop:
      # we specify the files to watch for changes
      watch:
        # it'll watch for changes in package.json and package-lock.json and rebuild the container if there are any changes
        - path: ./frontend/package.json
          action: rebuild
        - path: ./frontend/package-lock.json
          action: rebuild
        # it'll watch for changes in the frontend directory and sync the changes with the container real time
        - path: ./frontend
          target: /app
          action: sync

  # define the api service/container
  api: 
    # api service depends on the db service so the db service will be started before the api service
    depends_on: 
      - db

    # specify the build context for the api service
    build: ./backend
    
    # specify the ports to expose for the api service
    # the first number is the port on the host machine
    # the second number is the port inside the container
    ports: 
      - 8000:8000

    # specify environment variables for the api service
    # for demo purposes, we're using a local mongodb instance
    environment: 
      DB_URL: mongodb://db/anime
    
    # establish docker compose watch mode for the api service
    develop:
      # specify the files to watch for changes
      watch:
        # it'll watch for changes in package.json and package-lock.json and rebuild the container and image if there are any changes
        - path: ./backend/package.json
          action: rebuild
        - path: ./backend/package-lock.json
          action: rebuild
        
        # it'll watch for changes in the backend directory and sync the changes with the container real time
        - path: ./backend
          target: /app
          action: sync

  # define the db service
  db:
    # specify the image to use for the db service from docker hub. If we have a custom image, we can specify that in this format
    # In the above two services, we're using the build context to build the image for the service from the Dockerfile so we specify the image as "build: ./frontend" or "build: ./backend".
    # but for the db service, we're using the image from docker hub so we specify the image as "image: mongo:latest"
    # you can find the image name and tag for mongodb from docker hub here: https://hub.docker.com/_/mongo
    image: mongo:latest

    # specify the ports to expose for the db service
    # generally, we do this in api service using mongodb atlas. But for demo purposes, we're using a local mongodb instance
    # usually, mongodb runs on port 27017. So we're exposing the port 27017 on the host machine and mapping it to the port 27017 inside the container
    ports:
      - 27017:27017

    # specify the volumes to mount for the db service
    # we're mounting the volume named "anime" inside the container at /data/db directory
    # this is done so that the data inside the mongodb container is persisted even if the container is stopped
    volumes:
      - anime:/data/db

# define the volumes to be used by the services
volumes:
  anime:
```

