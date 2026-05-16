# Nano.Azure.Kubernetes.CertManager

> _Cert Manager deployment for managing SSL certificates in Nano applications._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[High Availability](#topology-affinity)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
* **[Dependencies](#dependencies)**  

## Summary
CertManager is an open-source Kubernetes add-on that automates the management and issuance of TLS certificates within a Kubernetes cluster. It simplifies the process of obtaining and renewing 
certificates from various Certificate Authorities (CAs) like Let's Encrypt and HashiCorp Vault. CertManager integrates seamlessly with Kubernetes, using custom resources to define and manage 
certificates, issuers, and certificate requests. It supports a range of certificate types and automates tasks such as certificate renewal and distribution, enhancing security and reducing 
administrative overhead. By automating these processes, CertManager helps ensure that applications and services within a Kubernetes environment maintain secure, up-to-date encryption.

> 📖 Learn more about **[Cert-Manager](https://cert-manager.io)** or the [Cert-Manager Helm Chart](https://artifacthub.io/packages/helm/cert-manager/cert-manager).  
> 📖 Also learn more about the **[Lets-Encrypt](https://letsencrypt.org)** certificate authority.

## Registration
This deployment provisions Cert-Manager in AKS.  

Before running the GitHub Action, add the following GitHub organization vars.  

| Secret                | Type  | Description                                                                                         |
| --------------------- | ----- | --------------------------------------------------------------------------------------------------- |
| `LETS_ENCRYPT_EMAIL`  | vars  | The email address used by Let’s Encrypt for certificate issuance notifications and failure alerts.  |

Here are a few useful `helm` commands.

```powershell
helm list -n $env:KUBERNETES_NAMESPACE;

helm status $env:APP_NAME -n $env:KUBERNETES_NAMESPACE

helm uninstall $env:APP_NAME -n $env:KUBERNETES_NAMESPACE
```

### High Availability
The Cert-Manger deployment is configured with Kubernetes pod anti-affinity and topology spread constraints to distribute replicas evenly across cluster nodes. This helps improve workload 
resilience and reduces the risk of multiple instances being impacted by a single node failure. The topology spread configuration also helps balance pod placement across the cluster while 
still allowing scheduling flexibility when resources are constrained.  

### Hardened Security
The Cert-Manager Helm chart is already hardened for production use by default, providing a secure baseline configuration out of the box. However, the `certmanager-values.yaml` file 
explicitly defines the chart’s default values to ensure that future changes or relaxations in the upstream chart configuration result in visible deployment or validation failures, rather 
'than silently introducing less secure container settings.

No additional overrides are required, as the container runs with restricted privileges and a minimized attack surface. This ensures a secure-by-default deployment suitable for production 
workloads.  

### Prometheus Monitoring
Monitoring is enabled for Prometheus and exposes a `/metrics` endpoint for scraping Cert-Manager runtime and scanning statistics. This allows integration with Kubernetes-native observability 
stacks such as Prometheus and Grafana for real-time visibility into scanner health, performance, and workload activity. Metrics can be used to detect anomalies, track scan throughput, and 
monitor resource consumption across replicas. This ensures CertManager operates as a fully observable security component within the cluster.  

Cert-manager uses a `ServiceMonitor` because its metrics are exposed through stable Kubernetes Services backed by long-running controller, webhook, and cainjector Deployments, which Prometheus 
is designed to scrape reliably via service endpoints. This avoids pod-level churn and ensures consistent monitoring even during rollouts, rescheduling, or scaling events.  

### Health Probes
The Cert-Manager Helm chart includes default readiness and liveness probes to ensure the service is properly initialized and remains operational. This deployment applies minor adjustments to 
the default probe configuration to improve stability and reduce the likelihood of unnecessary restarts during startup and normal operation.

These probes help Kubernetes manage pod lifecycle events, automatically restarting unhealthy instances and preventing traffic from being routed to unready pods.  

## Dependencies
Cert-Manager has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                            | Description                          | 
| ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**  | The Azure Kubernetes Service (AKS).  |
| **[Nano.Azure.Dns](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Dns/README.md#nanoazuredns)**                       | The Azure DNS Service.               |
