# Nano.Azure.Kubernetes.RabbitMQ

> _RabbitMQ cluster for Nano applications providing reliable messaging._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[High Availability](#topology-affinity)**  
  * **[Durable Quorum Queues](#durable-quorum-queues)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
  * **[Horizontal Pod Autoscaler](#horizontal-pod-autoscaler)**  
* **[Dependencies](#dependencies)**  

## Summary
RabbitMQ is an open-source message broker software that facilitates communication between applications by allowing them to send and receive messages asynchronously. It uses a queue-based 
architecture, where messages are published to queues and then consumed by one or more consumers. RabbitMQ supports various messaging protocols, including AMQP (Advanced Message Queuing 
Protocol), making it highly flexible and suitable for a wide range of use cases. It's widely used for building scalable, distributed systems and microservices due to its reliability, fault 
tolerance, and ability to handle large volumes of messages efficiently.  

> 📖 Learn more about **[RabbitMQ Cluster Operator](https://www.rabbitmq.com/kubernetes/operator/operator-overview)**.

## Registration
This deployment provisions a RabbitMQ cluster in AKS.  

Before running the GitHub Action, add the following GitHub organization secrets.  

| Secret                                     | Type    | Description                                                |
| ------------------------------------------ | ------- | ---------------------------------------------------------- |
| `{{environment}}_RABBITMQ_ADMIN_USERNAME`  | secrets | The username of the primary RabbitMQ admin user.           |
| `{{environment}}_RABBITMQ_ADMIN_PASSWORD`  | secrets | The password of the primary RabbitMQ admin user.           |
| `{{environment}}_RABBITMQ_ERLANG_COOKIE`   | secrets | The Erlang cookie used for cluster authentication.         |

To access the RabbitMQ cluster locally, use port-forwarding to expose the management UI by running the following command.

```powershell
kubectl port-forward $env:APP_NAME-cluster-0 15672 -n $env:KUBERNETES_NAMESPACE;
```

To retrieve the deployed RabbitMQ cluster from the Custom Resource Definition (CRD), run.  

```powershell
kubectl get rabbitmqclusters -n {{namespace}};
```

### High Availability
The RabbitMQ deployment is configured with Kubernetes pod anti-affinity rules to encourage replicas to be scheduled across different cluster nodes. This helps improve workload availability 
and resilience by reducing the risk of multiple RabbitMQ pods being affected by a single node failure. The affinity configuration uses the Kubernetes hostname topology key to distribute 
pods across the cluster whenever possible.  

### Durable Quorum Queues 
Each pod is provisioned with a 10Gi persistent volume to ensure durable message storage for queues.

Quorum queues are enabled for the cluster. This improves resiliency, consistency, and high availability across nodes. Be aware that quorum queues use leader-based replication, meaning 
re-elections may occur during node restarts, which can temporarily impact availability.

### Hardened Security
The security context is hardened for production use. Privilege escalation is disabled, and all Linux capabilities are dropped to minimize the container’s attack surface.

### Prometheus Monitoring
The RabbitMQ cluster is integrated with Prometheus monitoring in Azure Kubernetes Service (AKS) using a `ServiceMonitor`, because its metrics are exposed through stable Service endpoints that 
ensure reliable scraping and consistent observability of the StatefulSet pods across restarts, rescheduling, and scaling events.

### Health Probes
The deployment configures startup, readiness, and liveness probes. These are intentionally set with conservative thresholds to allow sufficient time for cluster stabilization and quorum 
leader re-election during startup or failover scenarios.

### Horizontal Pod Autoscaler
A Horizontal Pod Autoscaler (HPA) is intentionally not configured for the RabbitMQ cluster. RabbitMQ is a stateful clustered service, and automatic scaling of broker nodes can cause 
unnecessary queue rebalancing, leader re-election, and temporary instability. To ensure predictable performance and stable quorum behavior, the cluster uses a fixed replica count. Scaling 
should instead be handled at the application or consumer level, where stateless workloads can safely scale horizontally.  

## Dependencies
RabbitMQ has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                            | Description                          | 
| ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**  | The Azure Kubernetes Service (AKS).  |
