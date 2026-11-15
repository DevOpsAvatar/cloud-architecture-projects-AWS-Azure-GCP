<picture><source media="(prefers-color-scheme: dark)" srcset="https://nextwork.ai/static/nextwork-logo-paper.svg" /><img src="https://nextwork.ai/static/nextwork-logo-leather.svg" alt="NextWork" width="300" /></picture>

# Set Up Kubernetes Deployment

**Project Link:** [View Project](https://nextwork.ai/projects/ca1e2f05-dd07-5e77-b83c-1e6a6516944c)

**Author:** Dennis Chigozirim

---

## Introducing Today's Project!

In this project, I launch a Kubernetes cluster on Amazon EKS, clone a backend application from GitHub, build a Docker image of that backend, and push the image to Amazon ECR. Along the way I troubleshoot installation and configuration errors.

### Launching the cluster

I launched an EC2 instance, set up `eksctl` and the instance's AWS credentials, and then created an EKS cluster.

### Tools and concepts

I used Amazon EKS, Git, Docker, Amazon ECR and AWS CloudFormation. The key steps were:

- Launching an EC2 instance and installing `eksctl` and `kubectl`.
- Running `eksctl create cluster` to provision the EKS cluster and worker nodes.
- Building a Docker image of a Flask backend.
- Authenticating Docker to ECR and pushing the image to an ECR repository.

### Project reflection

This project took me about 1.5 to 2 hours. The most challenging part was troubleshooting the terminal hanging while it waited for CloudFormation, and installing `kubectl` after the cluster was built. My favourite part was running `kubectl get nodes` and seeing three `Ready` worker nodes, which meant the cluster was working.

I learned how `eksctl` uses CloudFormation behind the scenes to provision a whole EKS environment, including the VPC and Auto Scaling groups. I also learned how to package a Flask backend into a Docker image and push it to ECR so Kubernetes can pull it.

I chose this project to understand how Kubernetes clusters are provisioned on AWS and how containerized applications are managed.

## What I'm deploying

I created the cluster with `eksctl create cluster` and these parameters:

- `--name nextwork-eks-cluster`
- `--node-type t3.micro`
- `--nodes 3`

The command provisioned the EKS control plane, set up the VPC networking, and created a managed node group with three worker nodes.

### I'm deploying an app's backend

A backend is the server-side part of an application. It handles logic, database interactions and API requests. Here it is a Flask app plus its Dockerfile and requirements file. I installed Git and cloned the `nextwork-flask-backend` repository onto my EC2 instance with `git clone`.

![What I'm deploying](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/ca1e2f05-dd07-5e77-b83c-1e6a6516944c_1ebb86c71)

## Building a container image

A container image packages the application code with its dependencies, libraries and runtime into one portable artifact, so the backend runs the same way wherever it is deployed. That avoids the "it works on my machine" problem, and it lets me push the image to ECR and deploy it to my EKS cluster.

When I first tried to build the image, I got a permissions error. The `docker` command needs access to the Docker daemon, and I was logged in as the default `ec2-user`, which didn't have it.

I fixed it by adding my user to the `docker` group:

```bash
sudo usermod -a -G docker ec2-user
```

Then I logged out and back in (or ran `newgrp docker`) so the change took effect. The `docker` group is a Linux group that is allowed to talk to the Docker daemon socket, so I can run Docker commands without `sudo` every time.

![Building a container image](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/ca1e2f05-dd07-5e77-b83c-1e6a6516944c_45e6c3de5)

## Container Registry

I used Amazon ECR to store the Docker image of my backend. ECR is a fully managed container registry that stores images securely and lets my EKS cluster pull them on demand. That is much better than copying the image onto every EC2 instance by hand.

A registry works well with Kubernetes because it is a central, secure place for images. Kubernetes references an image by its registry URL, so every node pulls the same, up-to-date version.

![Container Registry](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/ca1e2f05-dd07-5e77-b83c-1e6a6516944c_l2m3n4o5)

## Extra: Backend Explained

The Flask app (`app.py`) acts as an API. It takes a search term from the user, passes it to an external search API, formats the JSON response and returns it. It shows how a backend handles requests, calls third-party services and returns data dynamically.

### Three key backend files

- **`requirements.txt`** lists the dependencies the app needs to run.
- **`Dockerfile`** tells Docker how to build the image. Its key commands are:
  - `RUN pip3 install -r requirements.txt` installs the Python dependencies.
  - `COPY . .` copies the application files into the container's `/app` directory.
  - `CMD ["python3", "app.py"]` starts the Flask app when the container runs.
- **`app.py`** is the main backend code, in three parts:
  1. **Setting up the app and routing:** it imports Flask and defines the route `/contents/<topic>`, which sends incoming requests to the `SearchContents` class. Users have to visit a URL ending in `/contents/<topic>` to see results.
  2. **Fetching data:** the `get()` method in `SearchContents` takes the topic from the URL, queries the Hacker News Search API, and collects details like `id`, `title` and `url`.
  3. **Sending the response:** the collected data is returned as formatted JSON.

---

*Project completed through [NextWork](https://nextwork.ai).*
