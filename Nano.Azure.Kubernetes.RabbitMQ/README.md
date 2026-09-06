# Nano.Azure.Kubernetes.RabbitMQ

> _RabbitMQ cluster for Nano applications providing reliable messaging queueing._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[High Availability](#high-availability)**  
  * **[Durable Quorum Queues](#durable-quorum-queues)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
  * **[Horizontal Pod Autoscaler](#horizontal-pod-autoscaler)**  
  * **[Azure Policy](#azure-policy)**  
* **[Dependencies](#dependencies)**  

## Summary
RabbitMQ is an open-source message broker software that facilitates communication between applications by allowing them to send and receive messages asynchronously. It uses a queue-based 
architecture, where messages are published to queues and then consumed by one or more consumers. RabbitMQ supports various messaging protocols, including AMQP (Advanced Message Queuing 
Protocol), making it highly flexible and suitable for a wide range of use cases. It's widely used for building scalable, distributed systems and microservices due to its reliability, fault 
tolerance, and ability to handle large volumes of messages efficiently.  

> 📖 Learn more about **[RabbitMQ Cluster Operator](https://www.rabbitmq.com/kubernetes/operator/operator-overview)**.

The RabbitMQ Cluster operator releases and version can be found here: **[Cluster Operator Releases](https://github.com/rabbitmq/cluster-operator/releases)**, and the RabbitMQ image version can 
be found here: **[Image Versions](https://hub.docker.com/_/rabbitmq)**.  

## Registration
This deployment provisions a RabbitMQ cluster in Kubernetes.  

RabbitMQ does not automatically scale down when the replica count is reduced. This is intentional, as removing nodes from a RabbitMQ cluster can disrupt quorum replicas and active cluster state. 
Before scaling down, ensure the cluster is healthy and fully synchronized.

To manually scale down the RabbitMQ cluster after reducing the replica count, execute the following command.

```powershell
kubectl scale statefulsets $env:APP_NAME-server --replicas=$env:KUBERNETES_REPLICA_COUNT -n $env:KUBERNETES_NAMESPACE
```

To access the RabbitMQ cluster locally, use port-forwarding to expose the management UI by running the following command.

```powershell
kubectl port-forward $env:APP_NAME-cluster-0 15672 -n $env:KUBERNETES_NAMESPACE;
```

To retrieve the deployed RabbitMQ cluster from the Custom Resource Definition (CRD), run.  

```powershell
kubectl get rabbitmqclusters -n $env:KUBERNETES_NAMESPACE;
```

To see the available configuration options for the deployment, use the following commands.  

```powershell
kubectl explain rabbitmqcluster;
```

### High Availability
The RabbitMQ deployment is configured with Kubernetes pod anti-affinity rules and topology spread constraints (automatics, not configurable) to encourage replicas to be scheduled across 
different cluster nodes. This helps improve workload availability and resilience by reducing the risk of multiple RabbitMQ pods being affected by a single node failure. The affinity 
configuration uses the Kubernetes hostname topology key to distribute pods across the cluster whenever possible.  

### Durable Quorum Queues 
Each pod is provisioned with a 10Gi persistent volume to ensure durable message storage for queues.

Quorum queues are enabled for the cluster. This improves resiliency, consistency, and high availability across nodes. Be aware that quorum queues use leader-based replication, meaning 
re-elections may occur during node restarts, which can temporarily impact availability.

### Hardened Security
The security context is hardened for production use. Privilege escalation is disabled, and the container is explicitly prevented from running as root (`runAsNonRoot: true`). All Linux 
capabilities are dropped to minimize the attack surface, and the filesystem is set to read-only to prevent any runtime modifications.

The container runs with a dedicated non-root user to enforce least-privilege access to mounted volumes. A runtime-default seccomp profile is applied to restrict system calls and further 
reduce exposure to kernel-level risks.  

RabbitMQ Operator requires access to the Kubernetes API and therefore cannot have auto-mounting of the Service Account token disabled. 

| Hardened Security             | Value | Description                                                                                                                            |
| ----------------------------- | ----- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Run As Non-Root               | ✔️    | Containers run as a non-root user.                                                                                                     |
| Non Privileged                | ✔️    | Privileged container mode is disabled.                                                                                                 |
| Disallow Privilege Escalation | ✔️    | Processes cannot gain additional privileges.                                                                                           |
| ReadOnly Root Filesystem      | (✔️)  | Root filesystem is mounted read-only. The Operator's `setup-container` uses `readOnlyRootFilesystem: false` and it cannot be changed.  |
| All Capabilities Dropped      | ✔️    | All Linux capabilities are removed by default.                                                                                         |
| Automount SA Token Disabled   | ✖️    | Service Account token auto-mounting is enabled, but the Operator reverts that during reconsiliation.                                   |

> ⚠️ Currently, the `automountServiceAccountToken: false` is ignored. RabbitMQ Operator overrides this setting and does not allow disabling it.

### Prometheus Monitoring
The RabbitMQ cluster is integrated with Prometheus monitoring in Azure Kubernetes Service (AKS) using a `ServiceMonitor`, because its metrics are exposed through stable Service endpoints that 
ensure reliable scraping and consistent observability of the StatefulSet pods across restarts, rescheduling, and scaling events.

> ⚠️ Azure Prometheus uses different CRDs: `azmonitoring.coreos.com/v1` instead of `monitoring.coreos.com/v1`.

### Health Probes
The deployment configures startup, readiness, and liveness probes. These are intentionally set with conservative thresholds to allow sufficient time for cluster stabilization and quorum 
leader re-election during startup or failover scenarios.

### Horizontal Pod Autoscaler
A Horizontal Pod Autoscaler (HPA) is intentionally not configured for the RabbitMQ cluster. RabbitMQ is a stateful clustered service, and automatic scaling of broker nodes can cause 
unnecessary queue rebalancing, leader re-election, and temporary instability. To ensure predictable performance and stable quorum behavior, the cluster uses a fixed replica count. Scaling 
should instead be handled at the application or consumer level, where stateless workloads can safely scale horizontally.  

### Azure Policy
The deployment updates the Azure Policy `allowedservicePortsInKubernetesClusterPorts` to permit the following ports.  

| Port  | Description                                                                 |
| ----- | --------------------------------------------------------------------------- |
| 5672  | AMQP protocol port used for messaging between clients and RabbitMQ.         |
| 15672 | RabbitMQ Management UI (HTTP dashboard) for administration and monitoring.  |
| 15692 | Prometheus metrics endpoint for RabbitMQ monitoring.                        |
| 4369  | Erlang Port Mapper Daemon (EPMD) used for node discovery in clusters.       |
| 25672 | Inter-node and clustering communication port for RabbitMQ nodes.            |

## Dependencies
RabbitMQ has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                   | Description                                  | 
| -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**         | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure/blob/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
