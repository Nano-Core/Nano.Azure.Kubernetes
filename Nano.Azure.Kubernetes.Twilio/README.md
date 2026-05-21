# Nano.Azure.Kubernetes.Twilio

> Twilio secret for use with sending SMS in Nano components and applications._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
* **[Dependencies](#dependencies)**  

## Summary
A Kubernetes secret storing a Twilio API key securely holds the API key within the cluster, allowing applications running in Kubernetes pods to send SMS via Twilio without exposing 
the key in code or environment variables. This approach ensures that the API key remains protected.  

> 📖 Learn more about **[Twilio](https://twilio.com)**.

Any SMS provider may be used; Twilio is only provided as an example. The primary purpose of this deployment is to create the `twilio-auth` Kubernetes secret, which can then 
be reused by other Nano components and applications by referencing the same secret.  

## Registration
This deployment creates a secret containing secrets for Twilio SMS.

Before running the GitHub Action, add the following GitHub organization vars and secrets.  

| Secret / Var                     | Type    | Description       |
| -------------------------------- | ------- | ----------------- |
| `{{environment}}_TWILIO_API_KEY` | secrets | Twilio API key.   |

## Dependencies
| Dependency                                                                                                                            | Description                          | 
| ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**  | The Azure Kubernetes Service (AKS).  |
