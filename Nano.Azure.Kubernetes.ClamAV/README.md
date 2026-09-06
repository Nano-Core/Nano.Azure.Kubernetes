# Nano.Azure.Kubernetes.ClamAV

> _Pluggable ClamAV file anti-virus scanner for Nano applications._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[Automated Virus Database Updates](#automated-virus-database-updates)**  
  * **[High Availability](#high-availability)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
  * **[Horizontal Pod Autoscaler](#horizontal-pod-autoscaler)**  
  * **[Azure Policy](#azure-policy)**  
* **[Dependencies](#dependencies)**  

## Summary
ClamAV is an open-source antivirus toolkit designed to detect and remove malware, viruses, and other malicious threats from files and systems. It provides a command-line scanner and a 
daemon for real-time scanning, offering comprehensive protection for a variety of operating systems. ClamAV uses a regularly updated virus definition database to identify threats, and 
it supports multiple file formats, including compressed and archived files. Its integration capabilities with other security tools and systems make it a versatile choice for enhancing 
cybersecurity. Widely used in both personal and enterprise environments, ClamAV is valued for its effectiveness and the support of a strong open-source community.

> 📖 Learn more about **[ClamAV](https://docs.clamav.net)** or the [ClamAV Helm Chart](https://artifacthub.io/packages/helm/wiremind/clamav).

## Registration
This deployment provisions ClamAV in Kubernetes.  

To connect to your ClamAV instance from outside the cluster execute the following commands.

```powershell
kubectl port-forward $env:APP_NAME-0 3310:3310;
```

And here are a few useful `helm` commands.

```powershell
helm list -n $env:KUBERNETES_NAMESPACE;

helm status $env:APP_NAME -n $env:KUBERNETES_NAMESPACE

helm uninstall $env:APP_NAME -n $env:KUBERNETES_NAMESPACE
```

### Automated Virus Database Updates
The ClamAV deployment uses _FreshClam_ to ensure virus definitions are continuously updated without manual intervention. Each pod runs `freshclam` in daemon mode, which periodically checks 
the official ClamAV mirrors **once every hour** for updated signature databases and downloads them directly into the `/data` persistent volume.  

This ensures that all ClamAV instances operate with up-to-date threat intelligence while maintaining consistency across the StatefulSet.

| Component           | Status     |
| ------------------- | ---------- |
| FreshClam running   | ✅ yes     |
| Auto updates        | ✅ enabled |
| Hourly checks       | ✅ enabled |
| Database download   | ✅ active  |
| Persistence (/data) | ✅ enabled |
| Clamd sync          | ✅ active  |
| Health checks       | ✅ OK      |

Overall, the deployment provides fully automated virus definition updates with no operational overhead, ensuring continuous protection and consistent scanning across all replicas.

### High Availability
The ClamAV deployment is configured with Kubernetes pod anti-affinity and topology spread constraints to distribute replicas evenly across cluster nodes. This helps improve workload 
resilience and reduces the risk of multiple instances being impacted by a single node failure. The topology spread configuration also helps balance pod placement across the cluster while 
still allowing scheduling flexibility when resources are constrained.  

### Hardened Security
The security context is hardened for production use. Privilege escalation is disabled, and the container is explicitly prevented from running as root (`runAsNonRoot: true`). All Linux 
capabilities are dropped to minimize the attack surface, and the filesystem is set to read-only to prevent any runtime modifications.

The container runs with a dedicated non-root user to enforce least-privilege access to mounted volumes. A runtime-default seccomp profile is applied to restrict system calls and further 
reduce exposure to kernel-level risks.  

ClamAV does not require access to the Kubernetes API. Auto-mounting of the Service Account token has been disabled.

| Hardened Security             | Value | Description                                        |
| ----------------------------- | ----- | -------------------------------------------------- |
| Run As Non-Root               | ✔️    | Containers run as a non-root user.                 |
| Non Privileged                | ✔️    | Privileged container mode is disabled.             |
| Disallow Privilege Escalation | ✔️    | Processes cannot gain additional privileges.       |
| ReadOnly Root Filesystem      | ✔️    | Root filesystem is mounted read-only.              |
| All Capabilities Dropped      | ✔️    | All Linux capabilities are removed by default.     |
| Automount SA Token Disabled   | ✔️    | Service Account token auto-mounting is disabled.   |

### Prometheus Monitoring
Monitoring is enabled for Prometheus and exposes a `/metrics` endpoint for scraping ClamAV runtime and scanning statistics. This allows integration with Kubernetes-native observability 
stacks such as Prometheus and Grafana for real-time visibility into scanner health, performance, and workload activity. Metrics can be used to detect anomalies, track scan throughput, and 
monitor resource consumption across replicas. This ensures ClamAV operates as a fully observable security component within the cluster.  

ClamAV uses a `ServiceMonitor` because its metrics are exposed through a stable Service endpoint, allowing Prometheus to reliably scrape them without depending on individual Pod lifecycles 
or IP changes.  

> ⚠️ Azure Prometheus uses different CRDs: `azmonitoring.coreos.com/v1` instead of `monitoring.coreos.com/v1`.

### Health Probes
The ClamAV Helm chart includes default startup, readiness, and liveness probes to ensure the scanner is correctly initialized and remains operational. These probes help Kubernetes manage 
pod lifecycle events, automatically restarting unhealthy instances and preventing traffic from being routed to unready pods.  

This deployment applies minor adjustments to the default probe configuration to improve stability and reduce the likelihood of unnecessary restarts during startup and normal operation.

### Horizontal Pod Autoscaler
The ClamAV deployment supports Horizontal Pod Autoscaling to dynamically adjust the number of replicas based on resource utilization. This ensures the scanner can scale out during 
increased workload demand and scale in when usage is low, maintaining efficiency and responsiveness. The autoscaling behavior helps stabilize performance while optimizing cluster 
resource usage.

To inspect the currently allocated resources or review the HPA configuration, use the following command.  

```powershell
kubectl describe hpa $env:APP_NAME -n $env:KUBERNETES_NAMESPACE;
```

### Azure Policy
The deployment updates the Azure Policy `allowedservicePortsInKubernetesClusterPorts` to permit the following ports.  

| Port | Description                                                            |
| ---- | ---------------------------------------------------------------------- |
| 3310 | ClamAV daemon (clamd) service port used for scanning requests.         |
| 9906 | ClamAV metrics endpoint used for Prometheus monitoring and scraping.   |

## Dependencies
ClamAV has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                   | Description                                  | 
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**         | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
