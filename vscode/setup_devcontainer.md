# **Setup a DevContainer**

A basic devcontainer config to get up and running with Python and Git in a DevContainer



<br><br>


---
**Contents**
- [**Setup a DevContainer**](#setup-a-devcontainer)
  - [**The `Dockerfile`**](#the-dockerfile)
  - [**The `devcontainer.json` config file**](#the-devcontainerjson-config-file)


---

## **The `Dockerfile`**

Create a `.devcontainer` in the project root, add a Dockerfile:

```dockerfile
# Use the base image for the API service
FROM ubuntu:jammy-20240416

# Enable terminal color support
RUN echo 'export TERM=xterm-256color' >> /etc/profile

WORKDIR /app

# Install Python 3.10 and dependencies
RUN apt-get update \
    && apt-get install -y software-properties-common \
    && add-apt-repository ppa:deadsnakes/ppa \
    && apt-get install -y python3.10 python3-pip

# Install additional tools and dependencies
RUN apt-get update && apt-get install -y \
    curl \
    git \
    && apt-get clean

RUN echo "alias python='python3'" >> /root/.bashrc

# Set the working directory
WORKDIR /workspace

```

## **The `devcontainer.json` config file**

Add the following file in `.devcontainer`: 

```json
{
  "name": "Bird Sound Classification",
  "dockerFile": "Dockerfile",
  "context": "..",
  "workspaceFolder": "/workspace",
  "build": {
    "args": {
      "USER_UID": "${localEnv:UID}",
      "USER_GID": "${localEnv:GID}"
    }
  },
  "settings": {
    "terminal.integrated.shell.linux": "/bin/bash"
  },
  "extensions": [
    "ms-python.python",
    "ms-azuretools.vscode-docker"
  ],
  "postCreateCommand": "pip install --upgrade pip && pip install --editable .",
  "mounts": [
    "source=${localWorkspaceFolder},target=/workspace,type=bind,consistency=cached",
    "source=/var/run/docker.sock,target=/var/run/docker.sock,type=bind"
  ]
}

```