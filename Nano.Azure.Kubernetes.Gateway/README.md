# Nano.Azure.Kubernetes.Gateway

> _The public gateway exposing Nano applications externally._

> ⚠️ This setup relies on features available in `aks-preview`.

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[API Gateway](#api-gateway)**  
  * **[Azure Application Load-Balancer](#azure-application-load-balancer)**  
  * **[SSL Certificate](#ssl-certificate)**  
* **[Dependencies](#dependencies)**  

## Summary
The Kubernetes Gateway API is a modern, extensible networking API designed to expose and route traffic to applications running in Kubernetes clusters. It introduces structured resources 
such as `GatewayClass`, `Gateway`, and `HTTPRoute`, providing a more flexible and role-oriented alternative to the traditional Ingress API with support for advanced routing, traffic 
splitting, and multi-protocol workloads.  

> 📖 Learn more about **[Kubernetes API Gateway](https://kubernetes.io/docs/concepts/services-networking/gateway/)** and check out the source on [GitHub Gateway Repository](https://github.com/kubernetes-sigs/gateway-api).  

## Registration
This deployment provisions an API Gateway and load balancer in Kubernetes, and also creates a TLS certificate.  

To retrieve the deployed public Gateway from the Custom Resource Definition (CRD), run.  

```powershell
kubectl get gateways -n {{namespace}};
```

And the deployed application load balancer.

```powershell
kubectl get ApplicationLoadBalancer -n {{namespace}};
```

### API Gateway
This configuration defines a Kubernetes `Gateway` that exposes applications over HTTPS on port 443 with TLS termination handled at the gateway. It uses the `azure-alb-external` GatewayClass, 
which integrates the Gateway API with Azure Application Gateway for Containers to manage external traffic routing into the cluster.  

The Gateway integrates with [Azure DNS](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Dns/README.md#nanoazuredns), providing stable public hostnames without the need to 
manually manage IP addresses. DNS records are automatically mapped to the address managed by the underlying Application Load Balancer, and multiple domain names are supported.  

The deployment is based on Microsoft’s guide **[Create Application Gateway for Containers managed by ALB Controller](https://learn.microsoft.com/en-us/azure/application-gateway/for-containers/quickstart-create-application-gateway-for-containers-managed-by-alb-controller)**.  

> ⚠️ Only a single `Gateway` should be deployed. These resources are managed by Azure.  

### Azure Application Load Balancer
The Application Load Balancer provides the underlying Azure-managed traffic distribution layer for Kubernetes ingress. It ensures external traffic is reliably routed into the cluster through 
the configured subnet and integrates with the Gateway API for application-level routing.  

This resource is managed by Azure and forms the foundation for inbound connectivity.  

> ⚠️ Only a single `ApplicationLoadBalancer` should be deployed. These resources are managed by Azure.  

### SSL Certificate
This resource defines a TLS certificate managed by cert-manager and issued via Let’s Encrypt using the configured ClusterIssuer. It automatically includes the specified domain names and handles 
renewal before expiration to ensure continuous HTTPS availability. The resulting certificate is stored as a Kubernetes secret and used by the Gateway for TLS termination.

The certificate is a wildcard certificate that covers all domains across the DNS zones managed in Nano.  

## Dependencies
Gateway has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                                                          | Description                                                                                  | 
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**                                                | The Azure Kubernetes Service (AKS).                                                          |
| **[Nano.Azure.Dns](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Dns/README.md#nanoazuredns)**                                                                     | Azure DNS maps external domains to the Kubernetes cluster for traffic routing.               |
| **[Nano.Azure.Kubernetes.CertManager](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.CertManager/README.md#nanoazurekubernetescertmanager)**  | Kubernetes Cert-Manager deployment responsible for issueing and managing SSL certificates.   |
