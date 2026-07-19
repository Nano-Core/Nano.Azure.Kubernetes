# Nano.Azure.Kubernetes.Firebase

> _Secret for use with firebase integration in Nano components and applications._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
* **[Dependencies](#dependencies)**  

## Summary
A Kubernetes secret storing a Firebase API key securely holds the API key within the cluster, allowing applications running in Kubernetes pods to use firebase without exposing the key in code 
or environment variables. This approach ensures that the API key remains protected.  

> 📖 Learn more about **[Google Firebase](https://firebase.google.com/)**.

## Registration
This deployment creates a secret containing secrets for Firebase.

Before running the GitHub Action, add the following GitHub organization vars and secrets.  

| Secret / Var                       | Type    | Description          |
| ---------------------------------- | ------- | -------------------- |
| `{{environment}}_FIREBASE_API_KEY` | secrets | Firebase API key.    |

## Dependencies
| Dependency                                                                                                                                   | Description                                  | 
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**         | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
