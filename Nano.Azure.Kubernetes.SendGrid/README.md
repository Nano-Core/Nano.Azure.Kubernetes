# Nano.Azure.Kubernetes.SendGrid

> _SendGrid secret for use with sending emails in Nano components and applications._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
* **[Dependencies](#dependencies)**  

## Summary
A Kubernetes secret storing a SendGrid API key securely holds the API key within the cluster, allowing applications running in Kubernetes pods to send emails via SendGrid without exposing 
the key in code or environment variables. This approach ensures that the API key remains protected while enabling seamless email integration.  

> 📖 Learn more about **[SendGrid](https://sendgrid.com)**.

Any SMTP compatible email provider may be used; SendGrid is only provided as an example. The primary purpose of this deployment is to create the `smtp-auth` Kubernetes secret, which can then 
be reused by other Nano components and applications by referencing the same secret.  

## Registration
This deployment creates a secret containing secrets for SendGrid email.

Before running the GitHub Action, add the following GitHub organization vars and secrets.  

| Secret / Var                       | Type    | Description                          |
| ---------------------------------- | ------- | ------------------------------------ |
| `SENDGRID_HOST`                    | secrets | SMTP host for SendGrid.              |
| `SENDGRID_USERNAME`                | secrets | SMTP username (`apikey`).            |
| `{{environment}}_SENDGRID_API_KEY` | secrets | SendGrid SMTP API key.               |
| `SENDGRID_SENDER_NAME`             | vars    | Sender display name for emails.      |
| `SENDGRID_SENDER_EMAIL`            | vars    | Sender email address for emails.     |

## Dependencies
| Dependency                                                                                                                            | Description                          | 
| ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**  | The Azure Kubernetes Service (AKS).  |
