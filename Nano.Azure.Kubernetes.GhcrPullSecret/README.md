# Nano.Azure.Kubernetes.GhcrPullSecret

> GHCR pull secrets used by Kubernetes to authenticate and pull container images._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
* **[Dependencies](#dependencies)**  

## Summary  
This repository automatically creates and refreshes a Kubernetes image pull secret for GitHub Container Registry (GHCR). The secret is used by workloads in the cluster to securely pull private 
container images without relying on long-lived credentials.

> ⚠️ **Important**  
> There is a known issue when using GitHub App installation tokens to pull images from GitHub Container Registry (GHCR).  
> As a workaround, use a Personal Access Token (PAT) from a dedicated bot account.
>  
> More details: **[Pulling from ghcr.io via app installation tokens is broken](https://github.com/orgs/community/discussions/171423)**.

## Registration  
This deployment creates the `ghcr-pull-secret`, which contains authentication information for GitHub Container Registry (GHCR). Authentication is performed using a GitHub App that generates 
short-lived installation tokens at runtime. These tokens are used to create or update the Kubernetes pull secret in an idempotent way.

The process runs on a schedule (every 50 minutes) to ensure tokens never expire, and also runs on pull requests and pushes to master. `Staging` is always updated for validation, while 
`Production` is only updated when changes are merged into master. This ensures both environments stay in sync with GHCR authentication requirements while keeping credentials fully automated 
and short-lived.

In Kubernetes deployment specs, add the following to use the `ghcr-pull-secret` for pulling images in your deployment:

```yaml
imagePullSecrets:
  - name: ghcr-pull-secret
```

## Dependencies
| Dependency                                                                                                                                   | Description                                  | 
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.GitHub.RunnerApp](https://github.com/Nano-Core/Nano.GitHub/tree/master/Nano.GitHub.RunnerApp/README.md#nanoazuregithubrunner)**      | The GitHub App.                              |
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**         | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
