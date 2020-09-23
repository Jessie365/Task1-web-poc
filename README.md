# Task 1: POC for web site

## Description
The purpose of the project is to deploy a very simple containerized web application on Kubernetes cluster. The app shows the number of visits on the site - each interaction would be counted as a new visit - no sessions are implemented. The application consists of two containers:
 * Web server implemented with the help of express.js. Source code and Dockerfile: [webapp](webapp)
 * Redis to store the visits count. The redis container is used only to demonstrate communication inside the cluster and some scaling considerations.

Note: A docker image for the web app is built and published in docker hub: jessie365/visits-webapp

![Web Application diagram](https://github.com/Jessie365/Task1-web-poc/raw/images/images/app.png)

## How to deploy
Terraform is used to provision an EKS cluster on AWS. Ingress controller is also provisioned by Terraform - there is no default ingress controller on EKS.

### Prerequisites
 * Terraform installed [download](https://www.terraform.io/downloads.html)
 * AWS account with IAM [permissions](https://github.com/terraform-aws-modules/terraform-aws-eks/blob/master/docs/iam-permissions.md)
 * Configured AWS CLI
 * AWS IAM Authenticator
 * [kubectl] (https://kubernetes.io/docs/tasks/tools/install-kubectl/)

### Deploy steps
```bash
# Provision the EKS cluster using Terraform
cd terraform
terraform init
terraform apply
# Deploy metrics-server (prerequisite for HPA)
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.7/components.yaml
# Deploy the application on the cluster
kubectl apply -f ../k8s
```

Execute the following command and try to open the URL under the ADDRESS column:
```
kubectl get ingress
```
![Get the application URL from the k8s ingress](https://raw.githubusercontent.com/Jessie365/Task1-web-poc/images/images/ingress.png)


### Scaling
Containerized applications most often use horizontal scaling (scaling out). For that purpose k8s HPA (Horizontal Pod Autoscaler) is used to control the number of pods of the web server - varying from 3 to 12 depending on the CPU usage. 

![Scaling the Web Application diagram](https://github.com/Jessie365/Task1-web-poc/raw/images/images/scaling.png)

## Next steps
 * Add environments: dev, qa, staging, prod
 * Configure CI/CD
 * Apply least priviledgе approach where possible (IAM, network policies)
 * Include monitoring solution with alarms
