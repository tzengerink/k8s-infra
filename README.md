# K8s Infra

Kubernetes infrastructure for personal projects.

## Installation

As a gateway [nginx-gateway-fabric](https://github.com/nginx/nginx-gateway-fabric) is used. Have a look at the documentation for the [installation steps](https://docs.nginx.com/nginx-gateway-fabric/install/manifests/), so the cluster supports the gateway.

## Configuration

### Gateway

For the configuration, the [documentation of the Gateway API](https://gateway-api.sigs.k8s.io/) can be used as a reference.

### SSL

For the SSL certificates, [cert-manager](https://cert-manager.io/docs/configuration/acme/) is used.

## Manual deploy

Use the GitHub UI to trigger the workflow, or use the CLI:

> gh workflow run deploy.yaml --ref main
