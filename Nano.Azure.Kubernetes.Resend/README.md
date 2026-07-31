# Nano.Azure.Kubernetes.Resend

> _Resend secret for use with sending emails in Nano components and applications._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
* **[Dependencies](#dependencies)**  

## Summary
A Kubernetes secret storing a Resend API key securely holds the API key within the cluster, allowing applications running in Kubernetes pods to send emails via Resend without exposing 
the key in code or environment variables. This approach ensures that the API key remains protected.  

> 📖 Learn more about **[Resend](https://resend.com)**.

Any SMTP compatible email provider may be used; Resend is only provided as an example. The primary purpose of this deployment is to create the `resend-auth` Kubernetes secret, which can 
then be reused by other Nano components and applications by referencing the same secret.  

## Registration
This deployment creates a secret containing secrets for Resend email.

Before running the GitHub Action, add the following GitHub organization vars and secrets.  

| Secret / Var                     | Type    | Description                          |
| -------------------------------- | ------- | ------------------------------------ |
| `{{environment}}_RESEND_API_KEY` | secrets | RESEND SMTP API key.                 |
| `RESEND_SENDER_NAME`             | vars    | Sender display name for emails.      |
| `RESEND_SENDER_EMAIL`            | vars    | Sender email address for emails.     |

## Dependencies
| Dependency                                                                                                                                   | Description                                  | 
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**         | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
