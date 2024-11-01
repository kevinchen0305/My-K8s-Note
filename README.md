# My-K8s-Note
Randomly record any Kubernetes notes 

## Prerequisites

### Install Docker
https://docs.docker.com/engine/install/ubuntu/
```bash
sudo usermod -aG docker ubuntu
```
```
docker --version
```

### Install kubectl
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux

### Install minikube
https://minikube.sigs.k8s.io/docs/start/
minkube start
```
minikube start --driver=docker --memory 12384 --cpus=4 --kubernetes-version v1.25.0
```
minikube enable ingress
```
minikube addons enable ingress
```

### Install Helm
wget desired version: https://github.com/helm/helm/releases
```
tar -zxvf helm-v3.0.0-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```
