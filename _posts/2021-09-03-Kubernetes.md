---
title: "Kubernetes"
categories:
  - Tec
tags:
  - Docker
  - Kubernetes
toc: true
---
Install Kubernetes and run minikuber on your local, and deploy spring cloud on kubernetes.

> How to install Docker on WSL2 <https://blog.sevendark.cn/tec/Docker/>

## Install kubectl On Linux

<https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/>

> kubectl reference <https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands>

## Install Minikube

<https://minikube.sigs.k8s.io/docs/start/>

### Enable Ingress

Enable ingress

```sh
minikube addons enable ingress
```

> if enable failed, add proxy
> How to config ssh proxy <https://blog.sevendark.cn/tec/SSH/#ssh-use-proxy>

Open tunnel, Need keep this command alive

```sh
minikube tunnel
```

then make sure your ingress config bind on host: `localhost`, and access them by `localhost`

> <https://github.com/kubernetes/ingress-nginx/blob/main/docs/deploy/index.md#docker-desktop>

## Kubernetes Compontents

- Service
<https://kubernetes.io/docs/concepts/services-networking/service/>

- Ingress
<https://kubernetes.io/docs/concepts/services-networking/ingress/>

## Some command

### check the logs

`-n` means namespace

```sh
kubectl logs --selector=app=zoomservice --tail 1 -n eb-services-qa
```

### get the pod status

```sh
kubectl describe pod  zoomservice-5bd9b8d5bb-8xsbf -n eb-services-qa
```

### get all things status in this namespace

```sh
kubectl get all --namespace=eb-services-qa
```

### get ingress status

```sh
kubectl get ingress -n eb-services-qa
```
