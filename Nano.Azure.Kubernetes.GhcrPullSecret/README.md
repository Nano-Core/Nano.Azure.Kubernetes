# Nano.Azure.Kubernetes.GhcrPullSecret

> GHCR pull secrets used by Kubernetes to pull container images._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
* **[Dependencies](#dependencies)**  

## Summary



📖 Learn how to configure access to **[GitHub Container Registry](https://github.com/Nano-Core/Nano.GitHub/tree/master/Nano.GitHub.ContainerRegistry)** to obtain the credentials needed to 
create a Kubernetes image-pull secret for pulling private images during GitHub Actions deployments.

## Registration
This step creates a Kubernetes image pull secret that allows the cluster to authenticate against the container registry and pull private images.  
This deployment creates a secret containing authentication variables for GitHub Container Registry (GHCR). The 

The secert use the GitHub App created to 
This secret is referenced by workloads that require access to images stored in the container registry.  
SHOW SNIPPET FROM DEPLOYMENT

The secret is updated on time-based

> ⚠️ The only remaining risk in both approaches is a very small timing window during secret replacement — and scheduled vs deployment-driven does NOT change the type of risk, only when it occurs.

## Dependencies
| Dependency                                                                                                                                   | Description                                  | 
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.GitHub.RunnerApp](https://github.com/Nano-Core/Nano.GitHub/tree/master/Nano.GitHub.RunnerApp/README.md#nanoazuregithubrunner)**      | The GitHub App.                              |
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**         | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
