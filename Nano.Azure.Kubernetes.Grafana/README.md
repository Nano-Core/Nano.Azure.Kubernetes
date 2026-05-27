# Nano.Azure.Kubernetes.Grafana

> _Grafana deployment for Nano applications, used for visualizing custom application business statistics._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[High Availability](#topology-affinity)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
  * **[Horizontal Pod Autoscaler](#horizontal-pod-autoscaler)**  
  * **[Grafana Sidecars](#grafana-sidecars)**  
  * **[SMTP Configuration](#smtp-configuration)**  
* **[Dependencies](#dependencies)**  

## Summary
Grafana is an open-source observability and analytics platform used to visualize, monitor, and analyze metrics, logs, and traces from multiple data sources. It provides customizable dashboards, 
real-time monitoring, and alerting, helping teams gain insight into infrastructure, applications, and system performance. With broad integrations and a flexible plugin ecosystem, Grafana is 
widely used for monitoring and troubleshooting modern, scalable environments.  

> 📖 Learn more about **[Grafana](https://grafana.com/docs)**.

Grafana integrates also well with Nano applications that expose data providers, enabling fast and flexible visualization of dashboards and operational statistics. With Nano Grafana, integrating 
observability into applications becomes simple and efficient, making it easy to build rich monitoring and analytics experiences with minimal setup.  

## Registration
This deployment provisions Grafana in Kubernetes.  

The deployment uses the [Grafana Community Helm Chart](https://artifacthub.io/packages/helm/grafana-community/grafana) to provision and manage the underlying infrastructure required 
for Grafana.  

Before running the GitHub Action, add the following GitHub organization secrets.  

| Secret                                    | Type    | Description                       |
| ----------------------------------------- | ------- | --------------------------------- |
| `{{environment}}_GRAFANA_ADMIN_USERNAME`  | secrets | The admin password for Grafana.   |
| `{{environment}}_GRAFANA_ADMIN_PASSWORD`  | secrets | The admin password for Grafana.   |

To access Grafana locally, use port-forwarding to expose it by running the following command.

```powershell
kubectl port-forward $env:APP_NAME 3000 -n $env:KUBERNETES_NAMESPACE;
```

Normally, this should not be necessary, as Grafana is already exposed externally.  

### High Availability
The Grafana deployment is configured with Kubernetes pod anti-affinity rules to encourage replicas to be scheduled across different cluster nodes. This helps improve workload availability 
and resilience by reducing the risk of multiple Redis pods being affected by a single node failure. The affinity configuration uses the Kubernetes hostname topology key to distribute 
pods across the cluster whenever possible.  

### Hardened Security
The security context is hardened for production use. Privilege escalation is disabled, and the container is explicitly prevented from running as root (`runAsNonRoot: true`). All Linux 
capabilities are dropped to minimize the attack surface, and the filesystem is set to read-only to prevent any runtime modifications.

The container runs with a dedicated non-root user to enforce least-privilege access to mounted volumes. A runtime-default seccomp profile is applied to restrict system calls and further 
reduce exposure to kernel-level risks.  

Grafana stores its persistent state primarily in `/var/lib/grafana`, which is backed by a PVC in Kubernetes. This includes plugins, cache, and optional embedded database data if no external 
database is used. Configuration and provisioning are mounted separately and are not persisted. Temporary runtime data is stored in `/tmp` and `/var/tmp` using ephemeral volumes.

### Prometheus Monitoring
The Grafana deployemnt is integrated with Prometheus monitoring in Azure Kubernetes Service (AKS) using a `ServiceMonitor`, because its metrics are exposed through stable Service endpoints 
that ensure reliable scraping and consistent observability of the StatefulSet pods across restarts, rescheduling, and scaling events.

> ⚠️ Azure Prometheus uses different CRDs: `azmonitoring.coreos.com/v1` instead of `monitoring.coreos.com/v1`.

### Health Probes
The deployment configures both a readiness and liveness probe.  

### Horizontal Pod Autoscaler
Horizontal Pod Autoscaling (HPA) is disabled by default, as it does not provide significant value for Grafana workloads. Grafana is primarily stateless, and scaling is typically handled more 
predictably through replica adjustments rather than reactive autoscaling based on CPU or memory metrics.

Scaling is therefore performed explicitly by updating the replica configuration, allowing for controlled and predictable capacity changes without relying on automatic scaling decisions.

Autoscaling can be enabled by adding the following section to `grafana-values.yaml`.  

```yaml
autoscaling:
  enabled: true
  minReplicas: 1
  maxReplicas: 5
  targetCPU: 180
  targetMemory: 180
```

### Grafana Sidecars
The Grafana sidecars automatically discover and load configuration from Kubernetes ConfigMaps and Secrets without requiring a Grafana restart.

| Sidecar       | Purpose                                                       |
| ------------- | ------------------------------------------------------------- |
| dashboards    | Automatically imports dashboards from ConfigMaps.             |
| datasources   | Automatically imports datasources from ConfigMaps/Secrets.    |

With `searchNamespace: ALL`, Grafana can watch the entire cluster for matching resources. This enables a “dashboards and datasources as code” approach, which is commonly used in GitOps-based 
Kubernetes environments.

> ⚠️ Dashboards and datasources are not included by default and must be created via ConfigMaps or Secrets. Sidecars only handle discovery and syncing, not provisioning.  

### SMTP Configuration
Grafana is configured with SMTP integration for sending emails such as password resets, alert notifications, and other system-generated messages. SMTP settings are provided via a Kubernetes 
Secret, which stores the mail server credentials and configuration securely.  

This allows Grafana to send emails for features such as account recovery and alerting workflows without exposing sensitive information in configuration files or environment variables.  

## Dependencies
Grafana has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                                                                           | Description                                  | 
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**                                                                 | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.MySql](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.MySql/README.md#nanoazuremysql)**                                                                                | The MySQL server.                            |
| **[Nano.Azure.Kubernetes.GitHubRunner](https://github.com/Nano-Core/Nano.Azure.Kubernetes.GitHubRunner/tree/master/Nano.Azure.Kubernetes.GitHubRunner/README.md#nanoazurekubernetesgithubrunner)**   | The GitHub Runner container job deployment.  |
| **[Nano.Azure.Kubernetes.Gateway](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.MySql/README.md#nanoazurekubernetesgateway)**                                                       | The Kubernetes Gateway deployment.           |
| **[Nano.Azure.Kubernetes.SendGrid](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.MySql/README.md#nanoazurekubernetessendgrid)**                                                     | The SendGrid secret deployment.              |
