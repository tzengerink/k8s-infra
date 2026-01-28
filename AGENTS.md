# AGENTS.md - Kubernetes Infrastructure Repository Guidelines

This document provides guidelines for agentic coding tools operating in this Kubernetes infrastructure repository.

## Repository Overview

This repository contains Kubernetes manifests for personal projects using:

- **Gateway API** (gateway.networking.k8s.io/v1)
- **NGINX Gateway Fabric** as the gateway controller
- **Kubernetes Deployments, Services, and Routes**

## Build/Lint/Test Commands

### Kubernetes Manifest Validation

```bash
# Validate all Kubernetes manifests
find . -name "*.yaml" -exec kubectl apply --dry-run=client -f {} \;

# Validate specific manifest
kubectl apply --dry-run=client -f <path-to-manifest.yaml>

# Check for syntax errors in YAML files
yamllint .
```

### Single Test Execution

```bash
# Test apply a single Kubernetes manifest
kubectl apply --dry-run=client -f <specific-manifest.yaml>

# Test deployment manifest specifically
kubectl apply --dry-run=client -f ./projects/<project>/deployment.yaml

# Test gateway configuration
kubectl apply --dry-run=client -f ./gateway/gateway.yaml
```

### Deployment Verification

```bash
# Verify all resources in production namespace
kubectl get all -n prd

# Check gateway resources
kubectl get gatewayclass -A
kubectl get gateway -n prd
kubectl get httproute -n prd

# Check deployment status
kubectl get deployments -n prd
kubectl get pods -n prd
```

## Code Style Guidelines

### File Organization

- **Gateway manifests**: `./gateway/` directory
- **Project manifests**: `./projects/<project-name>/` directories
- **Naming convention**: Use lowercase with hyphens for filenames
- **File extensions**: Use `.yaml` (not `.yml`)

### YAML Formatting

```yaml
# Standard structure for all manifests
apiVersion: <group>/<version>
kind: <ResourceKind>
metadata:
  name: <resource-name>
  namespace: <namespace> # Required for namespaced resources
  labels:
    app: <app-name> # Consistent labeling
spec:
  # Resource-specific specification
```

### Naming Conventions

- **Resource names**: Use lowercase with hyphens (kebab-case)
- **Labels**: Use `app: <app-name>` format
- **Namespaces**: Use short, descriptive names like `prd` for production
- **Gateway resources**: Use `shared-gateway` pattern for shared infrastructure

### Kubernetes Best Practices

1. **Immutable fields**: Never modify `apiVersion`, `kind`, or `metadata.name` after creation
2. **Resource limits**: Include CPU/memory limits in deployment specs
3. **Health checks**: Add liveness and readiness probes
4. **Environment variables**: Use for configuration, not secrets
5. **Replicas**: Always specify replica count (even if 1)

### Error Handling

- **Validation errors**: Always run `kubectl apply --dry-run=client` before applying
- **Missing fields**: Ensure all required fields are present
- **Version compatibility**: Verify API versions match cluster capabilities
- **Namespace conflicts**: Check for existing resources before applying

### Import and Dependency Management

- **No external dependencies**: This is a pure Kubernetes manifest repository
- **Image references**: Use full image paths with tags (not `latest`)
- **Controller references**: Specify exact controller names

### Type Safety

- **API versions**: Always use fully qualified API versions
- **Resource types**: Use exact kind names from Kubernetes API
- **Field types**: Follow Kubernetes OpenAPI schema types

### Security Guidelines

- **No secrets in manifests**: Use Kubernetes Secrets or external secret management
- **RBAC**: Apply least privilege principles
- **Network policies**: Include when needed for security
- **Image security**: Use trusted base images

### Example Patterns

#### Deployment Pattern

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <app-name>-app
  namespace: prd
  labels:
    app: <app-name>-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: <app-name>-app
  template:
    metadata:
      labels:
        app: <app-name>-app
    spec:
      containers:
        - name: <app-name>-app
          image: <registry>/<image>:<tag>
          ports:
            - containerPort: <port>
          env:
            - name: NODE_ENV
              value: "production"
```

#### Gateway Pattern

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: shared-gateway
  namespace: prd
spec:
  gatewayClassName: nginx
  listeners:
    - name: http
      protocol: HTTP
      port: 80
      allowedRoutes:
        namespaces:
          from: Same
```

## CI/CD Pipeline

The repository uses GitHub Actions for deployment:

- **Trigger**: Pushes to `main` branch or manual workflow dispatch
- **Environment**: Requires `KUBE_CONFIG` secret for cluster access
- **Process**:
  1. Create namespace if not exists
  2. Apply gateway manifests
  3. Apply all project manifests
  4. Verify deployment status

## Development Workflow

1. **Create feature branch**: `git checkout -b feat/<feature-name>`
2. **Add new manifests**: Follow naming and structure conventions
3. **Validate locally**: `kubectl apply --dry-run=client -f <manifest>.yaml`
4. **Test changes**: Apply to staging environment if available
5. **Commit changes**: Use descriptive commit messages
6. **Create PR**: Merge to main for production deployment

## Agent-Specific Instructions

- **File creation**: Only create Kubernetes manifest files (.yaml)
- **Validation**: Always validate manifests before suggesting changes
- **Error handling**: Provide specific error messages for manifest issues
- **Best practices**: Follow Kubernetes API conventions and patterns
- **Security**: Never suggest including secrets in manifests

## Common Commands for Agents

```bash
# List all Kubernetes manifests
find . -name "*.yaml" | sort

# Validate all manifests
find . -name "*.yaml" -exec kubectl apply --dry-run=client -f {} \;

# Check for YAML syntax errors
yamllint . || echo "yamllint not installed, skipping"

# Show current Kubernetes context
kubectl config current-context

# Get cluster info
kubectl cluster-info
```

## Troubleshooting Guide

### Common Issues

1. **API version not supported**: Check cluster capabilities with `kubectl api-versions`
2. **Missing required fields**: Compare with Kubernetes API documentation
3. **Namespace conflicts**: Use `kubectl get <resource> -n <namespace>` to check existing resources
4. **Image pull errors**: Verify image exists and is accessible
5. **Permission issues**: Check RBAC rules and service account permissions

### Debugging Commands

```bash
# Describe problematic resource
kubectl describe <resource-type> <resource-name> -n <namespace>

# Check events for errors
kubectl get events -n <namespace>

# View logs for troubleshooting
kubectl logs <pod-name> -n <namespace>

# Check resource status
kubectl get <resource-type> -n <namespace> -o yaml
```

This AGENTS.md file provides comprehensive guidelines for working with this Kubernetes infrastructure repository, covering build/lint/test commands, code style conventions, and operational best practices.

