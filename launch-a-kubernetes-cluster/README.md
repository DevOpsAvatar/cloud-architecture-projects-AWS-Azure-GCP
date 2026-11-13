<picture><source media="(prefers-color-scheme: dark)" srcset="https://nextwork.ai/static/nextwork-logo-paper.svg" /><img src="https://nextwork.ai/static/nextwork-logo-leather.svg" alt="NextWork" width="300" /></picture>

# Launch a Kubernetes Cluster

**Project Link:** [View Project](https://nextwork.ai/projects/aa8525df-82a0-5697-8721-a03b2db2c635)

**Author:** Dennis Chigozirim

---

## Introducing Today's Project!

In this project, I launch and connect to an EC2 instance so I can create my own Kubernetes cluster on Amazon EKS. I monitor the cluster creation with CloudFormation, access the cluster using an IAM access entry, and finally test the cluster's resilience.

### Don't forget to delete your resources!

Amazon EKS is not AWS Free Tier eligible. AWS charges $0.10 for every hour an EKS cluster is running. To keep costs down, I deleted all my resources the same day.

### Launching the EC2 instance

I launched an EC2 instance so I could connect to it and start sending commands.

### How I would improve my security group settings

I kept the default security group for my development instance to make connecting over SSH easier. Next time I would restrict SSH access to my own IP address instead of allowing connections from anywhere (`0.0.0.0/0`).

### Creating the cluster with eksctl

I tried to create an EKS cluster (and ran into an error), then installed `eksctl`, a tool for creating Kubernetes clusters.

## What is Kubernetes?

Kubernetes is a container orchestration platform, which is a fancy way of saying it coordinates containers so they run smoothly across all your servers. It makes sure containers run where they should, scales them automatically to meet demand, and restarts them if something crashes. Companies and developers use it to keep large, container-based applications steady, and it scales easily with traffic.

I ran the `eksctl create cluster` command to create an EKS cluster and its node group. The command defined the cluster name, Region and node type, and `eksctl` automatically created the underlying CloudFormation stack and IAM roles.

I ran into two errors while using `eksctl`:

1. **Missing IAM policy.** I had not attached the correct IAM policy to the EC2 instance (for example, `AmazonEKSClusterPolicy` was missing), so cluster creation failed because of insufficient permissions.
2. **Missing node group permissions.** The IAM role on the EC2 instance did not have permission to create managed node groups, so `eksctl` failed when calling `iam:PassRole` or creating node group resources, which produced an `AccessDenied` or `UnauthorizedOperation` error.

I then used CloudFormation to track how the cluster was being created.

![What is Kubernetes?](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_ff9bfc221)

### Setting up the IAM role

I set up my EC2 instance's IAM role and created the EKS cluster with `eksctl`. The error I expected next was an IAM permission error (`AccessDenied`), and that is what I got.

## eksctl and CloudFormation

CloudFormation helped create my EKS cluster because `eksctl` uses it as an infrastructure-as-code engine behind the scenes. When I ran `eksctl create cluster`, it generated and deployed CloudFormation stacks (like `eksctl-nextwork-eks-cluster-cluster` and the node group stack) to provision the underlying AWS resources, such as the EKS control plane, IAM roles and the managed node group.

It also created VPC resources, because `eksctl` provisions a dedicated, EKS-optimized VPC for the cluster by default. CloudFormation created the public and private subnets, route tables, internet gateways and security groups the cluster and its worker nodes need to communicate securely.

There was a second CloudFormation stack for the node group, which is a group of EC2 instances running the containers. A cluster is made up of nodes (the servers that actually run your containers) and a control plane (the brain that decides things like when to create or shut down containers).

Within a cluster, nodes are organized into node groups. Node groups let you manage multiple nodes together, so you can control settings like instance type and resource limits for the whole group instead of adjusting each node individually. This makes it simpler to scale and configure nodes for specific tasks.

![eksctl and CloudFormation](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_w3e4r5t6)

## The EKS console

I opened the EKS console and granted myself permission to interact with the cluster.

I had to create an IAM access entry so my IAM user or role could use `kubectl` against the new cluster. An access entry maps an AWS IAM identity to Kubernetes RBAC (role-based access control) permissions inside the cluster. I set it up by associating my IAM role with the cluster and attaching an access policy (such as `AmazonEKSClusterAdminPolicy`) to the entry, which let me authenticate and manage the cluster.

Creating the cluster took roughly 50 minutes. I'll create this cluster again in the next project of the series, so I could speed things up by using a pre-configured cluster template, deploying it with infrastructure as code (such as Terraform) with pre-baked AMIs, or using a smaller node group to cut the provisioning time of the worker nodes.

![The EKS console](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_e5f6g7h8)

## Extra: Deleting nodes

A cluster's nodes show up in Amazon EC2, because they are ordinary EC2 instances.

- **Desired size:** the number of nodes you want running in the node group.
- **Minimum size:** the fewest nodes the group needs to keep your app available at all times, even in low-demand periods.
- **Maximum size:** the most nodes you allow in the group. This also lets EKS scale the node group up from the desired size when demand is high.

When I deleted my EC2 instances, the managed node group detected the missing instances and launched new ones to restore the desired node count. This happens because a managed node group is controlled by an Auto Scaling Group (ASG), which continuously monitors the number and health of nodes. When the desired capacity isn't met, it provisions new instances so the cluster keeps the specified number of worker nodes.

![Extra: Deleting nodes](https://nextwork.ai/vibrant_orange_loyal_hoiho/uploads/aa8525df-82a0-5697-8721-a03b2db2c635_q7r8s9t0)

---

*Project completed through [NextWork](https://nextwork.ai).*
