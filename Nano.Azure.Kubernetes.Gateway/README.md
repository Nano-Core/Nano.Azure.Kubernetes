# Nano.Azure.Kubernetes.Gateway

> _The public gateway exposing Nano applications externally._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[API Gateway](#api-gateway)**  
  * **[SSL Certificate](#ssl-certificate)**  
  * **[TLS Policy](#tls-policy)**  
  * **[DNS Records](#dns-records)**  
* **[Dependencies](#dependencies)**  

## Summary
The Kubernetes Gateway API is a modern, extensible networking API designed to expose and route traffic to applications running in Kubernetes clusters. It introduces structured resources 
such as `GatewayClass`, `Gateway`, and `HTTPRoute`, providing a more flexible and role-oriented alternative to the traditional Ingress API with support for advanced routing, traffic 
splitting, and multi-protocol workloads.  

> 📖 Learn more about **[Kubernetes API Gateway](https://kubernetes.io/docs/concepts/services-networking/gateway/)** and check out the source on [GitHub Gateway Repository](https://github.com/kubernetes-sigs/gateway-api).  

> ⚠️ This setup currently relies on features available in `aks-preview`.

## Registration
This deployment provisions an API Gateway and load balancer in Kubernetes, and also creates a TLS certificate.  

To retrieve the deployed public Gateway from the Custom Resource Definition (CRD), run.  

```powershell
kubectl get gateways -n $env:KUBERNETES_NAMESPACE;
```

### API Gateway
This configuration defines a Kubernetes `Gateway` that exposes applications over HTTPS on port 443 with TLS termination handled at the gateway. It uses the `azure-alb-external` GatewayClass, 
which integrates the Gateway API with Azure Application Gateway for Containers to manage external traffic routing into the cluster.  

The Gateway integrates with **[Azure DNS](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.DNS/README.md#nanoazuredns)**, providing stable public hostnames without the need to 
manually manage IP addresses. DNS records are automatically mapped to the address managed by the underlying Application Load Balancer (ALB), configured as part of the 
**[Azure Kubernetes Cluster Deployment](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**

Multiple domain names are supported.  

> ⚠️ Only a single `Gateway` should be deployed.  

### SSL Certificate
This resource defines a TLS certificate managed by cert-manager and issued via Let’s Encrypt using the configured ClusterIssuer. It automatically includes the specified domain names and handles 
renewal before expiration to ensure continuous HTTPS availability. The resulting certificate is stored as a Kubernetes secret and used by the Gateway for TLS termination.

The certificate is a wildcard certificate that covers all domains across the DNS zones managed in Nano.  

### DNS Records
The DNS configuration ensures that each Azure DNS zone automatically resolves the wildcard domain (`*.<zone>`) to the Azure Application Load Balancer frontend endpoint. This provides a 
consistent public entry point for all applications without requiring manual DNS management per service.  

The setup is idempotent, meaning records are created only when missing and safely reused otherwise.

### TLS Policy
TLS is configured through a `FrontendTLSPolicy` CRD applied to the public gateway listener. The policy uses the predefined 2023-06-S strict profile, enforcing a minimum of TLS 1.2 while allowing 
TLS 1.2 and 1.3 only. It restricts cipher suites to ECDHE with GCM encryption, ensuring perfect forward secrecy via P-256 and P-384 curves.

This hardened configuration achieves an A+ rating on SSL Labs and Mozilla Observatory. Unlike the default Azure ALB 2023-06 policy, which still includes CBC-based cipher suites, 
the strict -S variant removes these weaker options and is required to reach the highest security grade.

> 📖 Learn more about **[Azure Gateway SSL Policy](https://learn.microsoft.com/en-us/azure/application-gateway/for-containers/tls-policy)**.

## Dependencies
Gateway has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                                                           | Description                                                                                  | 
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**                                                 | The Azure Kubernetes Service (AKS).                                                          |
| **[Nano.Azure.Dns](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.DNS/README.md#nanoazuredns)**                                                                      | Azure DNS maps external domains to the Kubernetes cluster for traffic routing.               |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**                                           | The GitHub Runner container job deployment.                                                  |
| **[Nano.Azure.Kubernetes.CertManager](https://github.com/Nano-Core/Nano.Azure.Kubernetes/blob/master/Nano.Azure.Kubernetes.CertManager/README.md#nanoazurekubernetescertmanager)**   | Kubernetes Cert-Manager deployment responsible for issueing and managing SSL certificates.   |
