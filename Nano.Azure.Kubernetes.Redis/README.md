# Nano.Azure.Kubernetes.Redis

> _Redis cluster for Nano applications providing and fast no-sql storage ._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[High Availability](#topology-affinity)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
  * **[Horizontal Pod Autoscaler](#horizontal-pod-autoscaler)**  
  * **[Azure Policy](#azure-policy)**  
* **[Dependencies](#dependencies)**  

## Summary
Redis is an in-memory data structure store, widely used as a distributed, scalable key-value database. It supports various data types like strings, hashes, lists, sets, and more, making it 
versatile for different use cases. Redis excels in performance due to its in-memory nature, making it ideal for caching, real-time analytics, session management, and message brokering. It also 
offers features like persistence, replication, and high availability through Redis Sentinel and Redis Cluster. With its simple and flexible design, Redis is a popular choice for building 
high-performance, scalable applications.  

> 📖 Learn more about **[Redis Cluster Operator](https://github.com/ot-container-kit/redis-operator)**.

## Registration
This deployment provisions a Redis cluster in Kubernetes.  

The deployment uses the [Redis Cluster Operator Helm Chart](https://artifacthub.io/packages/helm/ot-container-kit/redis-operator) to provision and manage the underlying infrastructure required 
for a Redis cluster. This operator is responsible for creating, configuring, and maintaining all Redis cluster components, ensuring a consistent and automated deployment model.

As part of the deployment configuration, explicit versioning is required for both the Redis container image and the Redis exporter image to ensure reproducibility and compatibility across 
environments. Available Redis image versions can be reviewed in the [Redis Image Releases](https://quay.io/repository/opstree/redis?tab=tags), while corresponding exporter versions are listed 
in the [Redis Exporter Images](https://quay.io/repository/opstree/redis-exporter?tab=tags).

Before running the GitHub Action, add the following GitHub organization secrets.  

| Secret                            | Type    | Description               |
| --------------------------------- | ------- | ------------------------- |
| `{{environment}}_REDIS_PASSWORD`  | secrets | The password for Redis.   |

To access the Redis cluster locally, use port-forwarding to expose the management UI by running the following command.

```powershell
kubectl port-forward $env:APP_NAME-leader-0 6379 -n $env:KUBERNETES_NAMESPACE;
```

To retrieve the deployed Redis cluster from the Custom Resource Definition (CRD), run.  

```powershell
kubectl get rabbitmqclusters -n {{namespace}};
```

To see the available configuration options for the deployment, use the following commands.  

```powershell
kubectl explain rediscluster;
```

### High Availability
The Redis deployment is configured with Kubernetes pod anti-affinity rules to encourage replicas to be scheduled across different cluster nodes. This helps improve workload availability 
and resilience by reducing the risk of multiple Redis pods being affected by a single node failure. The affinity configuration uses the Kubernetes hostname topology key to distribute 
pods across the cluster whenever possible.  

### Hardened Security
The security context is hardened for production use. Privilege escalation is disabled, and the container is explicitly prevented from running as root (`runAsNonRoot: true`). All Linux 
capabilities are dropped to minimize the attack surface, and the filesystem is set to read-only to prevent any runtime modifications.

The container runs with a dedicated non-root user to enforce least-privilege access to mounted volumes. A runtime-default seccomp profile is applied to restrict system calls and further 
reduce exposure to kernel-level risks.  

Redis Operator requires access to the Kubernetes API and therefore cannot have auto-mounting of the Service Account token disabled.  

| Hardened Security             | Value | Description                                                                                  |
| ----------------------------- | ----- | -------------------------------------------------------------------------------------------- |
| Run As Non-Root               | ✔️    | Containers run as a non-root user.                                                           |
| Non Privileged                | ✔️    | Privileged container mode is disabled.                                                       |
| Disallow Privilege Escalation | ✔️    | Processes cannot gain additional privileges.                                                 |
| ReadOnly Root Filesystem      | ✔️    | Root filesystem is mounted read-only.                                                        |
| All Capabilities Dropped      | ✔️    | All Linux capabilities are removed by default.                                               |
| Automount SA Token Disabled   | ✖️    | The chart does not supoport disable auto-mounting of the SA token, and it remains enabled.   |

### Prometheus Monitoring
The Redis cluster is integrated with Prometheus monitoring in Azure Kubernetes Service (AKS) using a `ServiceMonitor`, because its metrics are exposed through stable Service endpoints that 
ensure reliable scraping and consistent observability of the StatefulSet pods across restarts, rescheduling, and scaling events.

> ⚠️ Azure Prometheus uses different CRDs: `azmonitoring.coreos.com/v1` instead of `monitoring.coreos.com/v1`.

### Health Probes
The deployment configures readiness, and liveness probes for both leaders and followers.  

### Horizontal Pod Autoscaler
Redis Cluster scaling is stateful and slot-based, meaning it cannot safely rely on Kubernetes HPA mechanisms. Adding or removing nodes requires resharding, where data must be redistributed 
across the cluster to maintain consistency and availability. As a result, simple CPU or memory autoscaling would not guarantee correct data placement and could lead to an unbalanced or 
unstable cluster.

For this reason, the Redis Operator does not support Kubernetes HPA for `RedisCluster`.  

Instead, scaling is performed explicitly by updating the `clusterSize` field in the `RedisCluster` specification. The operator then handles the full lifecycle of the change, including 
provisioning new pods, joining them to the cluster, and redistributing hash slots to ensure even data distribution and cluster health.  

### Azure Policy
The deployment updates the Azure Policy `allowedservicePortsInKubernetesClusterPorts` to permit the following ports.  

| Port | Description                                                                 |
| ---- | --------------------------------------------------------------------------- |
| 6379 | Default Redis server port used for client connections and data operations.  |
| 9121 | Redis exporter metrics endpoint for Prometheus monitoring.                  |

## Dependencies
Redis has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                   | Description                                  | 
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**         | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
