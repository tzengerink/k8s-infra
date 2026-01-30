# K8s Infra

Kubernetes infrastructure for personal projects.

## Installation

As a gateway [nginx-gateway-fabric](https://github.com/nginx/nginx-gateway-fabric) is used. Have a look at the documentation for the [installation steps](https://docs.nginx.com/nginx-gateway-fabric/install/manifests/), so the cluster supports the gateway.

## Configuration

A couple of steps are needed to install the ingress controller and the certificate manager in the cluster.

### Install ingress-nginx

> kubectl create ns ingress-nginx
> kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.14.2/deploy/static/provider/cloud/deploy.yaml

For more info see the [official repository](https://github.com/kubernetes/ingress-nginx)

### Install cert-manager

> kubectl create namespace cert-manager
> kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.19.2/cert-manager.crds.yaml
> kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.19.2/cert-manager.yaml

For more info see the [official repository](https://github.com/cert-manager/cert-manager)

## Manual deploy

Use the GitHub UI to trigger the workflow, or use the CLI:

> gh workflow run deploy.yaml --ref main
