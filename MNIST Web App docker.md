## 1. Create Dockerfile and .dockerignore

```bash
MNIST_Web_APP/
├── Dockerfile        ✅ this is the file name
├── app.py
├── requirements.txt
├── .dockerignore
└── ...
```
## 2. Dockerfile template

```bash
# syntax=docker/dockerfile:1

# 1. Base image
FROM python:3.10-slim

# 2. Set working directory
WORKDIR /app

# 3. Copy files
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# 4. Expose port (change if your app uses a different one)
EXPOSE 5000

# 5. Run the app
CMD ["python", "app.py"]
```

```
# syntax=docker/dockerfile:1
```
- It tells Docker which build syntax and features to use.

- Specifically, it refers to the "BuildKit" frontend, which enables advanced Dockerfile capabilities like:

	- RUN --mount=type=cache (to speed up builds)
	
	- COPY --from (multi-stage builds)
	
	- faster parallel steps and improved performance
	
```
WORKDIR /app
```
- It **does not require** that `/app` already exists on your system or inside the container.

## 3. .dockerignore template

```
__pycache__/
*.pyc
.env
node_modules/
.git
```

## 4. Build docker image
```
docker build -t mnist-web-app .
```

## 5. Run docker container
```
docker run --rm -it -p 5000:5000 mnist-web-app
```
###  Image vs. Container

- `mnist-web-app` is the **image** — a blueprint.
    
- `docker run` creates a **container** from that image and starts it.
    

So this command means:

> “Take the image called `mnist-web-app`, create a container from it, and run it with the following settings.”

---

### What each flag does:

|Part|Meaning|
|---|---|
|`docker run`|Start a new container from an image.|
|`--rm`|Automatically **delete the container** after it stops (no leftover junk).|
|`-it`|Run interactively with a terminal (so you can see logs/output live).|
|`-p 5000:5000`|Forward **port 5000 in the container** to **port 5000 on your computer** — lets you open `http://localhost:5000`.|
|`mnist-web-app`|The name of the image to run.|