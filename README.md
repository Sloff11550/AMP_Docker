# 🐳 Custom AMP Docker Image

![Docker](https://img.shields.io/badge/Docker-ready-blue?logo=docker)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![Status](https://img.shields.io/badge/status-stable-brightgreen)

A simple and lightweight Docker image for running **AMP (Application Management Panel)**. Perfect for hosting your game servers and managing them with ease on platforms like **TrueNAS SCALE, Unraid, or any Linux/Windows host with Docker**.

---

## ✨ Features

- ✅ **Minimal footprint** using Debian slim base
- ✅ **AMP auto-install** inside the container
- ✅ **Headless mode ready** for server environments
- ✅ **Persistent storage support**
- ✅ Easy setup with **Docker Compose**
- ✅ Works great with **TrueNAS SCALE** and **Unraid**

---

## 📚 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [Step 1: Create the Dockerfile](#-step-1-create-the-dockerfile)
3. [Step 2: Build the Docker Image](#-step-2-build-the-docker-image)
4. [Step 3: Run the AMP Container](#-step-3-run-the-amp-container)
5. [Optional: Docker Compose](#-optional-docker-compose)
6. [Notes for TrueNAS SCALE](#%EF%B8%8F-notes-for-truenas-scale)
7. [Quick Tips](#-quick-tips)
8. [License](#-license)

---

## ✅ Prerequisites

- **Docker** installed on your system (TrueNAS SCALE, Unraid, Linux, or Windows)
- **Optional:** Docker Compose for easier container management
- Basic knowledge of using terminal/command line

---

## 📝 Step 1: Create the Dockerfile

Create a folder for your project (e.g., `AMP-Docker`) and add a file named **Dockerfile** with the following content:

<pre><code class="language-dockerfile">FROM debian:stable-slim

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    curl wget unzip libicu67 libssl1.1 libcurl4 libkrb5-3 libgssapi-krb5-2 ca-certificates \
    &amp;&amp; rm -rf /var/lib/apt/lists/*

RUN useradd -m ampuser
WORKDIR /home/ampuser

RUN wget https://cubecoders.com/downloads/amp/AMP_Linux_x64.tar.gz -O amp.tar.gz \
    &amp;&amp; tar -xzf amp.tar.gz \
    &amp;&amp; rm amp.tar.gz

RUN chown -R ampuser:ampuser /home/ampuser

USER ampuser

EXPOSE 8080

CMD ["./AMP", "--headless"]
</code></pre>

This block supports one-click copy on GitHub.

---

## 🏗 Step 2: Build the Docker Image

Navigate to the folder containing the Dockerfile and build the image:

<pre><code class="language-bash">cd /path/to/AMP-Docker

docker build -t custom-amp .
</code></pre>

This will create a Docker image named `custom-amp`.

---

## 🚀 Step 3: Run the AMP Container

Run the container with persistent storage and port mapping:

<pre><code class="language-bash">docker run -d \
  -p 8080:8080 \
  -v /path/to/ampdata:/home/ampuser/.config/CubeCoders/AMP \
  --name amp-server \
  custom-amp
</code></pre>

- `-p 8080:8080` maps AMP’s web UI to port 8080.
- `-v /path/to/ampdata` ensures AMP configs & data persist even if the container is removed.

Access the UI via `http://localhost:8080` or your server’s IP.

---

## 📦 Optional: Docker Compose

For easier management, create a **docker-compose.yml**:

<pre><code class="language-yaml">version: '3.8'

services:
  amp-server:
    image: custom-amp:latest
    container_name: amp-server
    ports:
      - "8080:8080"
    volumes:
      - /path/to/ampdata:/home/ampuser/.config/CubeCoders/AMP
    restart: unless-stopped
    environment:
      - TZ=America/Chicago
</code></pre>

Then run:

<pre><code class="language-bash">docker-compose up -d
</code></pre>

---

## 🖥️ Notes for TrueNAS SCALE

- TrueNAS SCALE has Docker (via Kubernetes) built-in.
- You can build and run this image directly through the **TrueNAS Shell**.
- For persistence, map volumes to your storage pools (e.g., `/mnt/tank/ampdata`).
- AMP UI will be accessible at `http://&lt;TrueNAS-IP&gt;:8080`.

---

## ✅ Quick Tips

- **Update** AMP by rebuilding the image and restarting the container.
- Use `docker logs -f amp-server` to view runtime logs.
- Use `docker-compose down` to stop the container.
- Always back up your mapped volume to preserve AMP configs.

---

## 📖 License

This project is licensed under the **MIT License**.

<pre><code class="language-text">MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
</code></pre>

---

> 💡 **Contributions welcome!** Feel free to open issues or PRs on GitHub to improve this Docker setup.
