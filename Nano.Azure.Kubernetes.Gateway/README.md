# Nano.Azure.Kubernetes.Gateway

> _The public gateway exposing Nano applications externally._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[API Gateway](#api-gateway)**  
  * **[Azure Load-Balancer](#azure-load-balancer)**  
  * **[SSL Certificate](#ssl-certificate)**  
* **[Dependencies](#dependencies)**  

## Summary
The Kubernetes Gateway API is a modern, extensible networking API designed to expose and route traffic to applications running in Kubernetes clusters. It introduces structured resources 
such as `GatewayClass`, `Gateway`, and `HTTPRoute`, providing a more flexible and role-oriented alternative to the traditional Ingress API with support for advanced routing, traffic 
splitting, and multi-protocol workloads.  

> 📖 Learn more about **[Kubernetes API Gateway](https://kubernetes.io/docs/concepts/services-networking/gateway/)** and the source on
[GitHub Gateway Repository](https://github.com/kubernetes-sigs/gateway-api).

> 📖 Learn more about **[Create Application Gateway for Containers managed by ALB Controller](https://learn.microsoft.com/en-us/azure/application-gateway/for-containers/quickstart-create-application-gateway-for-containers-managed-by-alb-controller)**.  


THIS USES PREVIEW az CLI


## Registration
This deployment provisions API Gateway in AKS.  

Before running the GitHub Action, add the following GitHub organization vars.  

| Secret                      | Type  | Description                                                                      |
| --------------------------- | ----- | -------------------------------------------------------------------------------- |
| `CERTIFICATE_ORGANIZATION`  | vars  | The organization owner of the certificate.                                       |

To retrieve the deployed public Gateway from the Custom Resource Definition (CRD), run.  

```powershell
kubectl get gateways -n {{namespace}};
```

### API Gateway



### Azure Load Balancer
This configuration defines a Kubernetes Gateway that exposes applications over HTTPS on port 443 using TLS termination at the gateway. It uses the `azure-application-lb` GatewayClass, which 
integrates the Gateway API with Azure’s Application Load Balancer to handle external traffic routing into the cluster.

The Gateway integrates with Azure DNS to provide a stable public hostname for the application, avoiding the need to manually manage or track IP addresses. DNS records point to the address 
managed by the `azure-application-lb` `GatewayClass`, ensuring traffic is reliably routed through Azure’s load balancing layer into the cluster.

### SSL Certificate

## Dependencies
Gateway has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                                                          | Description                                                                                  | 
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**                                                | The Azure Kubernetes Service (AKS).                                                          |
| **[Nano.Azure.Dns](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Dns/README.md#nanoazuredns)**                                                                     | Azure DNS maps external domains to the Kubernetes cluster for traffic routing.               |
| **[Nano.Azure.Kubernetes.CertManager](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.CertManager/README.md#nanoazurekubernetescertmanager)**  | Kubernetes Cert-Manager deployment responsible for issueing and managing SSL certificates.   |
