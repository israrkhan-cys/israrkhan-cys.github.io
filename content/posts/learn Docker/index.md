---
title: "Dockerizing a Flask App.A Beginner's Guide 🐳"
date: 2026-06-24
draft: false
tags: ["docker", "containers", "flask", "beginner"]
summary: "Dockerizing a small Flask app — writing the Dockerfile, building the image, and running it as a container."
coverImage: "cover.png"
keywords: ["docker", "flask", "containerization", "beginner tutorial"]
---

I recently learn about Containers, Dockers it was fascinating technology and i had previous project a Flask webApp so i go on dockerizing it to practice this Docker technology.

> Note: In the whole i have used docker cli not docker Deskop you can also used that it is mcuh easier to see which docker images you have you can start and stop them.

## What is Docker?

Docker is an open platform for building and shipping applications as containers. It 
isolates your application from the underlying hardware and operating system, so it 
runs identically everywhere.

This solves the classic **"it works on my machine"** problem. Whether you're working alone or in a team, Docker guarantees the app runs the same way across all machines.

## What You Need

- Docker installed on your machine
- A Flask app with a `requirements.txt` fileBreaking it
- A `Dockerfile` (i have written this below)

## Writing the Dockerfile

A Dockerfile is a plain text file with instructions Docker reads top to bottom to 
build your image. No extension, just `Dockerfile`.

Here is the complete Dockerfile for the Flask app:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
ENV PORT=5000
EXPOSE 5000
CMD ["python", "app.py"]
```

Breaking it down line by line:

- `FROM python:3.11-slim` — Start with a Linux system that already has Python installed.
- `WORKDIR /app` — Create and move into the `/app` directory inside the container.
- `COPY requirements.txt .` — Copy the dependencies list in first.
- `RUN pip install -r requirements.txt` — Install all dependencies.
- `COPY . .` — Now copy all project files in.
- `ENV PORT=5000` — Set the port as an environment variable the app can read.
- `EXPOSE 5000` — Documents that the app listens on port 5000.
- `CMD ["python", "app.py"]` — When the container starts, run the app.


## Building the Image

With the Dockerfile ready, build the image:

```bash
docker build -t flask-app .
```

The `-t` flag gives your image a name. The `.` tells Docker to look for the Dockerfile in the current directory.

You should see Docker pulling the base image, installing dependencies, and copying your files each step becoming a cached layer.

## Running the Container

```bash
docker run --name flask-app -p 5000:5000 flask-app
```

- `--name flask-app` — gives the container a name so you can reference it easily
- `-p 5000:5000` — maps port 5000 on your machine to port 5000 inside the container
s
Without the `-p` flag the app runs inside the container but you cannot reach it 
from your browser.

To Stop the running Container we use the cammand 


## Resources
- [Docker Official Docs](https://docs.docker.com)
- [Docker Hub](https://hub.docker.com) — official base images
- [Play with Docker](https://labs.play-with-docker.com) — free browser-based Docker playground
- [python:3.11-slim on Docker Hub](https://hub.docker.com/_/python)
  
