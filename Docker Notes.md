### [The Only Docker Tutorial You Need To Get Started](https://www.youtube.com/watch?v=DQdB7wFEygo&utm_source=chatgpt.com)


## 🎯 1. What is Docker & Why Use It?

- **Docker** lets you package applications into **containers**—lightweight, portable, and isolated environments. Unlike full virtual machines, containers share the host OS kernel, making them fast and efficient. ([youtube.com](https://www.youtube.com/watch?v=8hCXlfaw_Wg&utm_source=chatgpt.com "Docker Crash Course for Absolute Beginners! - YouTube"), [docker-curriculum.com](https://docker-curriculum.com/?utm_source=chatgpt.com "A Docker Tutorial for Beginners"))
    

---

## 🛠 2. Getting Started – Install & Verify

- Install **Docker Desktop** (Mac/Windows) or Docker Engine (Linux).
    
- Verify the installation with:
    
    ```bash
    docker run hello-world
    ```
    
    This downloads a test image and confirms Docker is working.
    
```bash
PS D:\Codess & Projects\Docker> docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 ```
    

---

## 📦 3. Working with Images & Containers

### * Pulling Images

- Get official images from Docker Hub:
    
    ```bash
    docker pull busybox
    ```
    

### * Running Containers

- Run a short command and exit:
    
    ```bash
    docker run busybox echo "hello from busybox"
    ```
    
- Launch an interactive shell:
    
    ```bash
    docker run -it busybox sh
    ```
    
- To stop a running Docker container:

	```bash
	docker stop <container_id_or_name>
	```

### * Listing Containers

- Show running only:
    
    ```bash
    docker ps
    ```
    
- Show all, including exited ones:
    
    ```bash
    docker ps -a
    ```
    

### * Cleaning Up

- Remove one or more stopped containers:
    
    ```bash
    docker rm <CONTAINER_ID> ...
    ```
    
- Auto-remove containers once they exit:
    
    ```bash
    docker run --rm busybox ...
    ```
    
- Remove all stopped containers in one go:
    
    ```bash
    docker container prune
    ```

- To kill the vmmem process run this as admin in cmd:
```
taskkill /f /im wslservice.exe
```

---

## 🌐 4. Hosting a Static Website with Docker

- Run a pre-built static site image:

```bash
docker run --rm -it prakhar1989/static-site
```

|Part|Meaning|
|---|---|
|`docker run`|Starts a new container from an image.|
|`--rm`|Automatically **removes** the container once it exits. Good for one-time runs.|
|`-it`|Combines two flags:• `-i` = interactive (keeps STDIN open)• `-t` = allocate a pseudo-TTY (terminal)Used together to allow interaction with the container (like a shell).|
|`prakhar1989/static-site`|The image to run (from Docker Hub). This is a prebuilt static website image.|
- Run in detached mode with random ports:

```bash
docker run -d -P --name static-site prakhar1989/static-site
docker port static-site
```

|Part|Meaning|
|---|---|
|`-d`|Run in **detached** mode (in the background).|
|`-P`|**Publish all exposed ports** of the container to **random ports** on the host.|
|`--name static-site`|Give the container a custom name ("static-site"). Makes it easier to refer to later.|
|`prakhar1989/static-site`|The Docker image used.|

```bash
docker port static-site
```

- Shows the **host port** that is mapped to the container’s exposed port.
    
- Example output: `80/tcp -> 0.0.0.0:49154` means your site is accessible at `http://localhost:49154`.
---
    
- Or specify host port mapping:

```bash
docker run -p 8888:80 prakhar1989/static-site
```

|Part|Meaning|
|---|---|
|`-p 8888:80`|**Manually map** port `80` in the container to port `8888` on your local machine.So, if the container serves a website on port 80, you access it via `http://localhost:8888`.|
|`prakhar1989/static-site`|The image name again.|

---

## 🧠 Summary Table

|Flag / Part|What it Does|
|---|---|
|`--rm`|Remove container after it exits|
|`-it`|Run interactively with terminal|
|`-d`|Detached mode (run in background)|
|`-P`|Randomly map exposed container ports to host ports|
|`-p HOST:CONTAINER`|Manually map host and container ports|
|`--name NAME`|Give container a custom name|
|`docker port NAME`|Show which ports were randomly assigned|

---

## 🐳 5. Step-by-Step: Build and Run a Flask App in Docker (With Full Explanations)

### 🧱 1. Create Your Application Files

Before working with Docker, make sure your Flask app directory has these files:

```
your-app/
├── app.py
├── requirements.txt
```

#### Example `app.py`:

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Docker + Flask!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

#### Example `requirements.txt`:

```
flask
```

---

### 🐋 2. Create the `Dockerfile`

A `Dockerfile` is a text file that tells Docker **how to build an image** step-by-step.

Here’s a beginner-friendly version with comments:

```dockerfile
# Step 1: Use an official Python image as the base
FROM python:3.8

# Step 2: Set the working directory inside the container
WORKDIR /usr/src/app

# Step 3: Copy all files from your local directory into the container
COPY . .

# Step 4: Install the required Python packages (Flask in this case)
RUN pip install --no-cache-dir -r requirements.txt

# Step 5: Expose port 5000 to the outside world (Flask default)
EXPOSE 5000

# Step 6: Define the command to run your app
CMD ["python", "./app.py"]
```

---

### 🔧 3. Build the Docker Image

Run the following command in the terminal (make sure you're inside your project folder):

```bash
docker build -t yourusername/catnip .
```

#### What each part means:

|Command Part|Explanation|
|---|---|
|`docker build`|Tells Docker to build a new image.|
|`-t yourusername/catnip`|Tags (names) the image as `yourusername/catnip` so you can refer to it later.|
|`.`|The dot (`.`) means "build from the current directory" where the Dockerfile is located.|

> ✅ Result: This command creates a Docker image that contains your Flask app, all its dependencies, and instructions on how to run it.

---

### 🚀 4. Run the Flask App Inside a Container

Now you can run the image inside a container:

```bash
docker run -p 8888:5000 yourusername/catnip
```

#### What this does:

|Part|Explanation|
|---|---|
|`docker run`|Starts a new container from your image.|
|`-p 8888:5000`|Maps **port 5000 inside the container** (where Flask is running) to **port 8888 on your host** (your browser/computer). You can now access the app via `http://localhost:8888`.|
|`yourusername/catnip`|The image you built earlier.|

---

### 🖥 5. Access Your Flask App

Once the container is running, open a web browser and go to:

```
http://localhost:8888
```

You should see:

```
Hello from Docker + Flask!
```

🎉 Congrats! You just created a Docker container that runs a complete Python Flask web app.

---

## 🔁 Bonus: Stop the Container

To stop it, press **Ctrl + C** in the terminal if running in the foreground.

If running in **detached mode** (with `-d`), use:

```bash
docker ps     # To find the container ID or name
docker stop <container_id_or_name>
```

---

## 🧼 Optional Clean-up

To remove the image if you no longer need it:

```bash
docker rmi yourusername/catnip
```

To remove all stopped containers:

```bash
docker container prune
```


---

## ☁️ 6. Deploy to AWS Elastic Beanstalk

- Use `Dockerrun.aws.json` or built image.
    
- EB auto-deploys your container for public access via AWS-managed infrastructure.
    

---

## 🔧 7. Multi-Container Apps with Docker Networking (Flask + Elasticsearch Example)

### 🧠 Why Multi-Container?

In real-world applications, you often need multiple services:

- A **web app** (Flask, Node.js, etc.)
    
- A **database** (PostgreSQL, MongoDB, etc.)
    
- A **search engine** (like Elasticsearch)  
    Instead of combining everything into one container, Docker encourages splitting them into **isolated containers** that **communicate over a network**.
    

---

## 🧪 Step-by-Step Guide:

---

### 🔁 1. Clone the Project

```bash
git clone https://github.com/prakhar1989/FoodTrucks
cd FoodTrucks
```

#### 📝 Explanation:

|Command|Meaning|
|---|---|
|`git clone`|Clones the GitHub repo to your local machine.|
|`cd FoodTrucks`|Navigates into the project folder where the Docker-related files are located.|

---

### 🛠 2. Build the Flask App Docker Image

```bash
docker build -t yourusername/foodtrucks-web .
```

#### 📝 Explanation:

|Part|Meaning|
|---|---|
|`docker build`|Command to create a new Docker image.|
|`-t yourusername/foodtrucks-web`|Tags the image with a name, which you'll use to run it later.|
|`.`|The `.` means Docker will look for the `Dockerfile` in the **current directory**.|

> 💡 `foodtrucks-web` is the **Flask-based frontend** for searching food truck data using Elasticsearch.

---

### 🌐 3. Create a Docker Network

```bash
docker network create foodtrucks-net
```

#### 📝 Explanation:

|Part|Meaning|
|---|---|
|`docker network create`|Creates a **custom Docker network** so containers can talk to each other.|
|`foodtrucks-net`|The name of the network you're creating.|

> 📢 This step is **crucial** for enabling communication between containers **by name** (like using `es` or `foodtrucks-web` as hostnames).

---

### 🚀 4. Launch Both Containers (Elasticsearch + Flask)

#### Run Elasticsearch container:

```bash
docker run -d \
  --name es \
  --net foodtrucks-net \
  -p 9200:9200 \
  -e "discovery.type=single-node" \
  docker.elastic.co/elasticsearch/elasticsearch:6.3.2
```

#### 📝 Explanation:

|Part|Description|
|---|---|
|`-d`|Run in background (detached).|
|`--name es`|Names the container `es` (used for networking).|
|`--net foodtrucks-net`|Connects this container to the custom network.|
|`-p 9200:9200`|Maps port 9200 of the container (Elasticsearch default) to your host.|
|`-e "discovery.type=single-node"`|Configures Elasticsearch to run in **single-node mode**, ideal for dev/testing.|
|`docker.elastic.co/elasticsearch/elasticsearch:6.3.2`|The official Elasticsearch image.|

---

#### Run Flask Web App container:

```bash
docker run -d \
  --name foodtrucks-web \
  --net foodtrucks-net \
  -p 5000:5000 \
  yourusername/foodtrucks-web
```

#### 📝 Explanation:

|Part|Description|
|---|---|
|`-d`|Run in detached mode (background).|
|`--name foodtrucks-web`|Names the container.|
|`--net foodtrucks-net`|Puts it on the **same network** as Elasticsearch so it can connect using `http://es:9200`.|
|`-p 5000:5000`|Maps port 5000 from the container (Flask default) to your host (access via `http://localhost:5000`).|
|`yourusername/foodtrucks-web`|The image you built earlier from the Flask app.|

---

### 🧠 How Do They Talk?

Since both containers are on the **same Docker network (`foodtrucks-net`)**, they can communicate with each other by **container name**, like:

```python
# Inside the Flask code:
ELASTICSEARCH_URL = "http://es:9200"
```

> ✅ This is possible because Docker sets up **internal DNS** for containers on the same network.

---

## 🧹 Optional: Check and Cleanup

### Check if containers are running:

```bash
docker ps
```

### Stop the containers:

```bash
docker stop foodtrucks-web es
```

### Remove the containers:

```bash
docker rm foodtrucks-web es
```

### Remove the network:

```bash
docker network rm foodtrucks-net
```

---

## 🧭 Summary Diagram (Conceptual View)

```
+-----------------------+   Network: foodtrucks-net    +--------------------------+
| foodtrucks-web        |  <-------------------------> | es (Elasticsearch)       |
| Flask App             |                              | REST API on port 9200    |
| Port 5000 exposed     |                              | discovery.type=singlenode|
+-----------------------+                              +--------------------------+
```

---

## 🚀 8. Docker Compose – Define & Launch Multi-Service Apps

## 🧩 What is Docker Compose?

**Docker Compose** allows you to define and run multi-container Docker applications using a single YAML file. Instead of typing long `docker run` commands, you describe services, networks, volumes, and configurations in a single file.

---

## 📁 Final Project Structure

```
FoodTrucks/
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
└── flask-app/
    └── app.py
```

> ✅ Note: The Flask code should be in the `flask-app/` directory, and Dockerfile is in the root.

---

## 🛠 docker-compose.yml (Full Explanation)

```yaml
version: "3.8"

services:
  es:
    image: docker.elastic.co/elasticsearch/elasticsearch:6.3.2
    container_name: es
    ports:
      - "9200:9200"
    environment:
      - discovery.type=single-node
    volumes:
      - esdata:/usr/share/elasticsearch/data

  web:
    build:
      context: .
    container_name: foodtrucks-web
    ports:
      - "5000:5000"
    depends_on:
      - es
    volumes:
      - ./flask-app:/usr/src/app

volumes:
  esdata:
```

---

## 🔍 Explanation of Each Section

### `version: "3.8"`

Specifies the Compose file format. Use version 3.8 for good compatibility.

---

### **Services** – The containers you're defining

---

### 🔹 `es` Service (Elasticsearch)

|Key|Description|
|---|---|
|`image`|Uses the official Elasticsearch image.|
|`container_name`|Names the container `es`.|
|`ports`|Maps port `9200` from container to host.|
|`environment`|Sets env vars (required to run single-node ES).|
|`volumes`|Persists data using a named volume (`esdata`).|

---

### 🔹 `web` Service (Flask App)

|Key|Description|
|---|---|
|`build.context`|Tells Docker to look in the current directory (`.`) for the Dockerfile.|
|`container_name`|Names the container `foodtrucks-web`.|
|`ports`|Maps port `5000` to your host for Flask.|
|`depends_on`|Ensures `es` service starts before `web`.|
|`volumes`|Mounts the local `./flask-app` folder into the container for live reload (optional).|

---

### **Volumes**

```yaml
volumes:
  esdata:
```

This creates a **named Docker volume** for storing persistent Elasticsearch data.

---

## 🏁 How to Run It

### ▶ Start all services

```bash
docker-compose up
```

This:

- Builds the Flask app (if not already built).
    
- Starts both `es` and `web` containers.
    
- Connects them on a shared internal network.
    

---

### ⏹ Stop and remove all containers

```bash
docker-compose down
```

---

### 🔁 Rebuild the images after changes

```bash
docker-compose up --build
```

---

## 🌐 Access the App

- Visit: [http://localhost:5000](http://localhost:5000/)
    
- Elasticsearch API: [http://localhost:9200](http://localhost:9200/)
    

---

## 🔧 Optional Additions

- Add `restart: always` under services for automatic restarts.
    
- Add `healthcheck` to make sure services start in order and are alive.
    

---

## ✅ Summary: Benefits of Docker Compose

|Feature|Benefit|
|---|---|
|Single YAML file|Define entire architecture in one place|
|Easy start/stop|`up` and `down` control full lifecycle|
|Shared network|Containers talk by name (`web`, `es`)|
|Reusability|Version-controlled and easy to share|

---
## 🐳 What is `docker init`?

### 🔧 `docker init` is a CLI tool (currently experimental) that **automatically generates Docker-related files** for your project.

It helps you:

- Create a `Dockerfile`
    
- Optionally create a `.dockerignore`
    
- Optionally create a `docker-compose.yml`
    
- Select the correct runtime environment (like Python, Node.js, Go, etc.)
    
- Specify which file to run, which ports to expose, and other settings
    

---

## 🧪 How to Use It

### Step 1: Go into your project directory:

```bash
cd my-flask-app
```

### Step 2: Run:

```bash
docker init
```

### Step 3: Answer prompts like:

```
✔ What application platform does your project use? › Python
✔ What version of Python do you want to use? › 3.10
✔ What is the command to run your app? › python app.py
✔ Which port does your app listen on? › 5000
✔ What do you want to name your service in docker-compose.yml? › web
```

---

## 🗂 What it Generates

|File|Purpose|
|---|---|
|`Dockerfile`|Defines how to build your app image|
|`.dockerignore`|Tells Docker which files to ignore (e.g., `.git`, `__pycache__`)|
|`docker-compose.yml` (optional)|Lets you define multi-container setups|
|`README.Docker.md`|Explains how to use the generated Docker config|

---

## 💡 Example `Dockerfile` Created by `docker init` (Python)

```dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 5000
CMD ["python", "app.py"]
```

---

## 📜 Example `docker-compose.yml`

```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "5000:5000"
```

---

## ✅ Benefits of Using `docker init`

|Feature|Benefit|
|---|---|
|Interactive prompts|No need to memorize Dockerfile syntax|
|Auto file creation|Saves time setting up from scratch|
|Best practices|Uses clean, slim base images|
|Compose-ready|Sets you up for scaling to multi-container apps|
|Language support|Works with Python, Node.js, Java, Go, etc.|

---

## ⚠️ Limitations & Notes

- Still **experimental** (as of Docker v24+), so you may need to enable it:
    
    ```bash
    export DOCKER_CLI_EXPERIMENTAL=enabled
    ```
    
- Not yet as customizable as writing your own `Dockerfile` for advanced apps.
    

---

## 🧠 When to Use It

Use `docker init` when:

- You're new to Docker and need a quick scaffold.
    
- You want to bootstrap a project fast.
    
- You’re working with common languages like Python, Node, Java, etc.
    

---

Would you like me to show how to integrate `docker init` output into the Flask + Elasticsearch project we discussed earlier?
### ✅ Quick Recap

|Concept|Purpose|
|---|---|
|Images|Immutable templates for containers|
|Containers|Running instances of images|
|Dockerfile|Defines how to build custom images|
|Networks|Enables communication between containers|
|Compose|Orchestrates multi-container applications|

---