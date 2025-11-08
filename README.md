# DEVOPS-PIPELINE

A collection of reusable GitHub Actions workflows for DevOps automation, including Docker image building and Kubernetes deployment.

## 📁 Project Structure

```
DEVOPS-PIPELINE/
├── .github/
│   └── workflows/
│       ├── build-docker-and-push-package.yaml
│       └── deploy-to-kubernetes.yaml
└── README.md
```

## 🚀 Available Workflows

### 1. Build Docker And Push Package

**File**: `.github/workflows/build-docker-and-push-package.yaml`

A reusable workflow that builds a Docker image and pushes it to the GitHub Container Registry (GHCR).

#### Usage Example

```yaml
jobs:
  build-and-deploy:
    uses: taninchot/DEVOPS-PIPELINE/.github/workflows/build-docker-and-push-package.yaml@main
    with:
      IMAGE_TAG: ghcr.io/your-username/your-repo:latest
    secrets:
      WORKFLOW_PAT: ${{ secrets.GITHUB_TOKEN }}
```

#### Inputs

| Input | Description | Required | Type |
|-------|-------------|----------|------|
| `IMAGE_TAG` | The tag for the Docker image to be built and pushed | Yes | string |

#### Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `WORKFLOW_PAT` | GitHub Personal Access Token with package registry permissions | Yes |

#### Features

- ✅ Multi-platform support (linux/amd64)
- ✅ Uses Docker Buildx for optimized builds
- ✅ Pushes to GitHub Container Registry (GHCR)
- ✅ Reusable workflow design

---

### 2. Deploy to Kubernetes

**File**: `.github/workflows/deploy-to-kubernetes.yaml`

A reusable workflow that deploys applications to Kubernetes clusters with rollout restart capabilities.

#### Usage Example

```yaml
jobs:
  deploy-to-k8s:
    uses: taninchot/DEVOPS-PIPELINE/.github/workflows/deploy-to-kubernetes.yaml@main
    with:
      K8S_NAMESPACE: production
      K8S_DEPLOYMENT_NAME: my-app
      WAIT_FOR_ROLLOUT: true
      TIMEOUT_MINUTES: 10
    secrets:
      KUBE_CONFIG_BASE64: ${{ secrets.KUBE_CONFIG }}
```

#### Inputs

| Input | Description | Required | Type | Default |
|-------|-------------|----------|------|---------|
| `K8S_NAMESPACE` | The Kubernetes namespace for the deployment | Yes | string | - |
| `K8S_DEPLOYMENT_NAME` | The name of the Kubernetes Deployment resource | Yes | string | - |
| `TIMEOUT_MINUTES` | Timeout for kubectl rollout status in minutes | No | number | 5 |
| `WAIT_FOR_ROLLOUT` | Wait for Kubernetes rollout and start the pod | No | boolean | false |

#### Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `KUBE_CONFIG_BASE64` | Base64 encoded kubeconfig file for Kubernetes cluster access | Yes |

#### Features

- ✅ Rollout restart deployment
- ✅ Optional rollout status waiting
- ✅ Configurable timeout
- ✅ Namespace isolation
- ✅ Secure kubeconfig handling

## 🔧 Prerequisites

### For Docker Workflow
- GitHub repository with Container Registry enabled
- Personal Access Token with `write:packages` permission

### For Kubernetes Workflow
- Kubernetes cluster access
- kubeconfig file with deployment permissions
- Base64 encoding of kubeconfig file

## 📝 How to Use These Workflows

### Step 1: Set Up Secrets

1. **For Docker workflow**: Create a `WORKFLOW_PAT` secret with your GitHub Personal Access Token
2. **For Kubernetes workflow**: Create a `KUBE_CONFIG_BASE64` secret with your base64-encoded kubeconfig

### Step 2: Create a Dockerfile

Your project should include a `Dockerfile` in the root directory for the build workflow to work.

### Step 3: Create a Calling Workflow

Create a workflow file in your project that calls these reusable workflows:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    uses: taninchot/DEVOPS-PIPELINE/.github/workflows/build-docker-and-push-package.yaml@main
    with:
      IMAGE_TAG: ghcr.io/${{ github.repository }}:${{ github.sha }}
    secrets:
      WORKFLOW_PAT: ${{ secrets.WORKFLOW_PAT }}

  deploy:
    needs: build
    uses: taninchot/DEVOPS-PIPELINE/.github/workflows/deploy-to-kubernetes.yaml@main
    with:
      K8S_NAMESPACE: production
      K8S_DEPLOYMENT_NAME: my-app
      WAIT_FOR_ROLLOUT: true
    secrets:
      KUBE_CONFIG_BASE64: ${{ secrets.KUBE_CONFIG_BASE64 }}
```

## 🔒 Security Considerations

- All sensitive information is handled through GitHub Secrets
- Kubeconfig files are never exposed in logs
- Temporary files are created with appropriate permissions
- No hardcoded credentials in workflow files

## 🤝 Contributing

Feel free to submit issues and enhancement requests!

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Related Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitHub Container Registry](https://docs.github.com/en/packages/guides/introduction-to-github-container-registry)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Docker Buildx](https://docs.docker.com/buildx/)

---

**Note**: These workflows are designed to be reusable components. Make sure to adjust the inputs and secrets according to your specific requirements.
