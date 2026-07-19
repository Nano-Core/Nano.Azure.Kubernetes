# Nano.Azure.Kubernetes.CertManager

> _Cert Manager deployment for managing SSL certificates for Nano applications._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[High Availability](#high-availability)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
  * **[Azure Policy](#azure-policy)**  
* **[Dependencies](#dependencies)**  

## Summary
CertManager is an open-source Kubernetes add-on that automates the management and issuance of TLS certificates within a Kubernetes cluster. It simplifies the process of obtaining and renewing 
certificates from various Certificate Authorities (CAs) like Let's Encrypt and HashiCorp Vault. CertManager integrates seamlessly with Kubernetes, using custom resources to define and manage 
certificates, issuers, and certificate requests. It supports a range of certificate types and automates tasks such as certificate renewal and distribution, enhancing security and reducing 
administrative overhead. By automating these processes, CertManager helps ensure that applications and services within a Kubernetes environment maintain secure, up-to-date encryption.

> 📖 Learn more about **[Cert-Manager](https://cert-manager.io)** and the **[Cert-Manager Helm Chart](https://artifacthub.io/packages/helm/cert-manager/cert-manager)**.  

To verify issued certificates, you can use **[crt.sh](https://crt.sh/)** by searching for `{domain-name}`.  
To assess the security level and configuration quality of a certificate, you can use the **[SSL Labs SSL Test](https://www.ssllabs.com/ssltest/analyze.html?d={domain-name}&hideResults=on)**.  

## Registration
This deployment provisions Cert-Manager in AKS.  

The deployment is based on the cert-manager tutorial **[Getting started with cert-manager on Azure Kubernetes Service (AKS) and Let’s Encrypt](https://cert-manager.io/docs/tutorials/getting-started-aks-letsencrypt)**, 
available in the official cert-manager documentation, and Microsoft’s guide **[Using cert-manager with Let’s Encrypt and Gateway API on Azure Application Gateway for Containers](https://learn.microsoft.com/en-us/azure/application-gateway/for-containers/how-to-cert-manager-lets-encrypt-gateway-api)**.  

It works in conjunction with **[Azure DNS](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.DNS/README.md#nanoazuredns)** to issue certificates using the DNS-01 challenge method and 
supports any number of domain names within a single certificate.  

Before running the GitHub Action, add the following GitHub organization secrets.  

| Secret                | Type     | Description                                                                                         |
| --------------------- | -------- | --------------------------------------------------------------------------------------------------- |
| `LETS_ENCRYPT_EMAIL`  | secrets  | The email address used by Let’s Encrypt for certificate issuance notifications and failure alerts.  |

Here are some useful `helm` commands for inspecting and managing the installation.  

```powershell
helm list -n $env:KUBERNETES_NAMESPACE;

helm status $env:APP_NAME -n $env:KUBERNETES_NAMESPACE

helm uninstall $env:APP_NAME -n $env:KUBERNETES_NAMESPACE
```

When monitoring certificate issuance, the following commands are useful for tracking progress and diagnosing failures.  

```powershell
kubectl get certificaterequests -n $env:KUBERNETES_NAMESPACE;

kubectl get orders -n $env:KUBERNETES_NAMESPACE;

kubectl get challenges -n $env:KUBERNETES_NAMESPACE;
```

### High Availability
The Cert-Manger deployment is configured with Kubernetes pod anti-affinity and topology spread constraints to distribute replicas evenly across cluster nodes. This helps improve workload 
resilience and reduces the risk of multiple instances being impacted by a single node failure. The topology spread configuration also helps balance pod placement across the cluster while 
still allowing scheduling flexibility when resources are constrained.  

### Hardened Security
The security context is hardened for production use. Privilege escalation is disabled, and the container is explicitly prevented from running as root (`runAsNonRoot: true`). All Linux 
capabilities are dropped to minimize the attack surface, and the filesystem is set to read-only to prevent any runtime modifications.

The container runs with a dedicated non-root user to enforce least-privilege access to mounted volumes. A runtime-default seccomp profile is applied to restrict system calls and further 
reduce exposure to kernel-level risks.  

Cert-Manager requires access to the Kubernetes API, and the service account token is auto-mounted.  

| Hardened Security             | Value | Description                                                              |
| ----------------------------- | ----- | ------------------------------------------------------------------------ |
| Run As Non-Root               | ✔️    | Containers run as a non-root user.                                       |
| Non Privileged                | ✔️    | Privileged container mode is disabled.                                   |
| Disallow Privilege Escalation | ✔️    | Processes cannot gain additional privileges.                             |
| ReadOnly Root Filesystem      | ✔️    | Root filesystem is mounted read-only.                                    |
| All Capabilities Dropped      | ✔️    | All Linux capabilities are removed by default.                           |
| Automount SA Token Disabled   | ✖️    | The chart requires auto-mounting the SA token, and it remains enabled.   |

### Prometheus Monitoring
Monitoring is enabled for Prometheus and exposes a `/metrics` endpoint for scraping Cert-Manager runtime and scanning statistics. This allows integration with Kubernetes-native observability 
stacks such as Prometheus and Grafana for real-time visibility into scanner health, performance, and workload activity. Metrics can be used to detect anomalies, track scan throughput, and 
monitor resource consumption across replicas. This ensures CertManager operates as a fully observable security component within the cluster.  

Cert-manager uses a `ServiceMonitor` because its metrics are exposed through stable Kubernetes Services backed by long-running controller, webhook, and cainjector Deployments, which Prometheus 
is designed to scrape reliably via service endpoints. This avoids pod-level churn and ensures consistent monitoring even during rollouts, rescheduling, or scaling events.  

> ⚠️ Azure Prometheus uses different CRDs: `azmonitoring.coreos.com/v1` instead of `monitoring.coreos.com/v1`.

### Health Probes
The Cert-Manager Helm chart includes default readiness and liveness probes to ensure the service is properly initialized and remains operational. This deployment applies minor adjustments to 
the default probe configuration to improve stability and reduce the likelihood of unnecessary restarts during startup and normal operation.

These probes help Kubernetes manage pod lifecycle events, automatically restarting unhealthy instances and preventing traffic from being routed to unready pods.  

### Azure Policy
The deployment updates the Azure Policy `allowedservicePortsInKubernetesClusterPorts` to permit the following ports.  

| Port | Description                                                                    |
| ---- | ------------------------------------------------------------------------------ |
| 443  | HTTPS API endpoint used by cert-manager webhooks and Kubernetes API access.    |
| 9402 | Metrics endpoint exposed by cert-manager controllers for Prometheus scraping.  |

## Dependencies
Cert-Manager has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                   | Description                                  | 
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**         | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.Dns](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.DNS/README.md#nanoazuredns)**                              | The Azure DNS Service.                       |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
