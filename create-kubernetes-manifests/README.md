<picture><source media="(prefers-color-scheme: dark)" srcset="https://nextwork.ai/static/nextwork-logo-paper.svg" /><img src="https://nextwork.ai/static/nextwork-logo-leather.svg" alt="NextWork" width="300" /></picture>

# Create Kubernetes Manifests

**Project Link:** [View Project](https://nextwork.ai/projects/ccc2aecf-ba9e-5ebe-98ec-e0c4aec9c4ea)

**Author:** Dennis Chigozirim

---

## Introducing Today's Project!

In this project, I create Kubernetes Deployment and Service manifests to learn how to deploy and expose a backend application in a Kubernetes cluster.

### Don't forget to delete your resources!

Amazon EKS is not AWS Free Tier eligible. AWS charges $0.10 for every hour an EKS cluster is running. To keep costs down, I deleted all my resources the same day.

### Launching the cluster

I launched and connected to an EC2 instance, set up `eksctl` and the instance's AWS credentials, and then created my EKS cluster.

### Tools and concepts

I used Amazon EKS, `eksctl` and Amazon ECR to provision a managed Kubernetes cluster and host my containerized application image. The key concept is using manifests to declaratively define how Kubernetes should deploy my backend, and exposing it to the network with a Service.

### Project reflection

I chose this project to get hands-on experience deploying a real application to Kubernetes and to see how EKS, ECR and Docker work together in a real workflow.

It took me about 2 hours. The most challenging parts were troubleshooting the initial CloudFormation stack error and working out how to exit the nano editor. My favourite part was building the Docker image and watching the YAML manifest create the pods.

## Project Set Up

### Kubernetes cluster

I launched a Kubernetes cluster by connecting to my EC2 instance with EC2 Instance Connect in the console, then running `eksctl create cluster` in the terminal. I specified:

- the cluster name (`nextwork-eks-cluster`)
- the node group name
- the instance type (`t3.micro`)
- the number of nodes
- the AWS Region (`eu-west-3`)

### Backend code

I installed Git on my EC2 instance and cloned the application code from GitHub with `git clone`, because I needed the source code on the server to build the container image. The backend is a Flask application made up of a `Dockerfile`, `app.py`, `requirements.txt` and a `README.md`.

### Container image

I built a container image so the application and its dependencies are packaged into one portable unit that can be deployed to Kubernetes. I used this command, which runs the Dockerfile's instructions step by step:

```bash
docker build -t nextwork-flask-backend .
```

### Pushing the image to Amazon ECR

Kubernetes needs a central place to pull the image from so it can run the application on any node in the cluster. To push the image to Amazon ECR, I:

1. Logged in with `aws ecr get-login-password`.
2. Tagged my local image with the ECR repository URI using `docker tag`.
3. Uploaded it with `docker push`.

## Manifest files

Kubernetes manifests are YAML files that define the desired state of resources in a cluster, such as Deployments and Services. They let you deploy applications in a declarative, repeatable and version-controlled way, instead of configuring everything by hand with commands.

A Kubernetes Deployment manages the lifecycle and scaling of your application's pods. It keeps the desired number of replicas running and replaces them if they fail. The container image URL in my Deployment manifest tells Kubernetes exactly which Docker image to pull from Amazon ECR and run in those pods.

![Manifest files](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/ccc2aecf-ba9e-5ebe-98ec-e0c4aec9c4ea_b01876554)

## Service Manifest

The Deployment only runs the pods inside the cluster. A Service gives them a stable IP address and DNS name and exposes the application, so users or other services can reach the backend over the network. The Service manifest defines how traffic is routed to the right pods based on their labels, and the port mapping (such as routing port 8080 to the backend).

My Service manifest creates a `NodePort` Service named `nextwork-flask-backend`:

- It uses the selector `app: nextwork-flask-backend` to route traffic to the pods created by my Deployment.
- It listens on port `8080` and forwards traffic to `targetPort: 8080` on the pods over TCP.

![Service Manifest](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/ccc2aecf-ba9e-5ebe-98ec-e0c4aec9c4ea_b01876555)

## Deployment Manifest

Annotating the Deployment manifest helped me understand what each YAML section (such as `spec`, `selector` and `template`) is for. Breaking the structure down into plain English showed me how Kubernetes uses these fields to manage pods and maintain the desired state of my application.

One part I still want to learn more about is the `strategy` section (such as `RollingUpdate`) and how health checks (liveness and readiness probes) work. I want to understand how Kubernetes achieves zero-downtime deployments and how it knows when a new pod is ready to receive traffic.

![Deployment Manifest](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/ccc2aecf-ba9e-5ebe-98ec-e0c4aec9c4ea_6aae73e71)

---

*Project completed through [NextWork](https://nextwork.ai).*
