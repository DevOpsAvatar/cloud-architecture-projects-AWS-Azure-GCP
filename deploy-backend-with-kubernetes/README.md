<picture><source media="(prefers-color-scheme: dark)" srcset="https://nextwork.ai/static/nextwork-logo-paper.svg" /><img src="https://nextwork.ai/static/nextwork-logo-leather.svg" alt="NextWork" width="300" /></picture>

# Deploy Backend with Kubernetes

**Project Link:** [View Project](https://nextwork.ai/projects/090a4709-f1f4-5d95-ae4d-b8199bf808ed)

**Author:** Dennis Chigozirim

---

## Introducing Today's Project!

In this project, I deploy a containerized application to an Amazon EKS cluster and expose it to the internet. I wanted to learn how to manage containerized workloads with Kubernetes on AWS and how services like ECR and EKS work together in a real-world architecture.

### Don't forget to delete your resources!

Amazon EKS is not AWS Free Tier eligible. AWS charges $0.10 for every hour an EKS cluster is running. To keep costs down, I deleted everything I created (the EKS cluster, the EC2 worker nodes and the ECR repository) as soon as I finished.

### Launching the cluster

I launched and connected to an EC2 instance, set up `eksctl` and the instance's AWS credentials, and then created my EKS cluster.

### Tools and concepts

I used Kubernetes, Amazon EKS, Amazon ECR, `kubectl` and Docker. The key concepts are:

- Using manifests to define the desired state of my application and deploy it to the cluster.
- How Kubernetes pulls container images from a registry like ECR.
- How Kubernetes manages the underlying worker nodes automatically.

### Project reflection

This project took me about 1.5 to 2 hours. The most challenging part was waiting for the EKS cluster to provision and troubleshooting missing tools like `kubectl` in the terminal. My favourite part was running `kubectl get nodes` and seeing all my worker nodes as `Ready`.

I chose this project to learn how to deploy containerized applications to a managed Kubernetes service on AWS.

## Project Set Up

### Kubernetes cluster

I connected to my EC2 instance over SSH and ran `eksctl create cluster` in the terminal. I specified:

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

Kubernetes needs a central place to pull the image from so it can run the application on any node in the cluster. To push the image to ECR, I:

1. Logged in with `aws ecr get-login-password`.
2. Tagged my local image with the ECR repository URI using `docker tag`.
3. Uploaded it with `docker push`.

## Manifest files

Kubernetes manifests are YAML files that define the desired state of resources in a cluster, such as Deployments and Services. They let you deploy applications in a declarative, repeatable and version-controlled way, instead of configuring everything by hand with commands.

A Kubernetes Deployment manages the lifecycle and scaling of your application's pods. It keeps the desired number of replicas running and replaces them if they fail. The container image URL in my Deployment manifest tells Kubernetes exactly which Docker image to pull from Amazon ECR and run in those pods.

A Kubernetes Service exposes your application to the network, with a stable IP address and DNS name so traffic can reach your pods. The Service manifest defines how that traffic is routed to the right pods based on their labels, and the port mapping (such as routing port 8080 to your backend).

![Manifest files](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/090a4709-f1f4-5d95-ae4d-b8199bf808ed_b01876554)

## Backend Deployment

I deployed the backend by running `kubectl apply -f` on both `flask-deployment.yaml` and `flask-service.yaml`. This sent my manifests to Kubernetes, which created the Deployment (to run the pods) and the Service (to expose the backend).

### kubectl

`kubectl` is the command-line tool for working with Kubernetes resources, such as Deployments and Services, once your cluster is running.

![Backend Deployment](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/090a4709-f1f4-5d95-ae4d-b8199bf808ed_6cfb382f2)

## Verifying Deployment

For the project extension, I used the EKS console to check visually that my cluster's nodes and the application's pods were running. The cluster needs explicit permissions before my AWS user or role can view its resources (such as nodes and pods) in the console, so I set up IAM access by adding the necessary policies (like `AmazonEKSViewPolicy` or `AmazonEKSClusterPolicy`) to my IAM user or role in the IAM console.

Once I had access, I found pods running inside each node. A pod is the smallest deployable unit in Kubernetes and represents one instance of a running process in the cluster. Containers in a pod share the same network namespace (an IP address and port space) and storage volumes, so they can communicate easily.

The EKS console shows each pod's events, which let me follow its lifecycle step by step: scheduled to a node, pulling the Docker image from ECR, and starting the container. That confirmed Kubernetes authenticated with ECR, downloaded my `nextwork-flask-backend` image and started the application without errors.

![Verifying Deployment](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/090a4709-f1f4-5d95-ae4d-b8199bf808ed_3b391f873)

---

*Project completed through [NextWork](https://nextwork.ai).*
