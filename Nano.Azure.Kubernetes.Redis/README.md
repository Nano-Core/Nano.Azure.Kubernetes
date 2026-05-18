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
* **[Dependencies](#dependencies)**  

## Summary
Redis is an in-memory data structure store, widely used as a distributed, scalable key-value database. It supports various data types like strings, hashes, lists, sets, and more, making it 
versatile for different use cases. Redis excels in performance due to its in-memory nature, making it ideal for caching, real-time analytics, session management, and message brokering. It also 
offers features like persistence, replication, and high availability through Redis Sentinel and Redis Cluster. With its simple and flexible design, Redis is a popular choice for building 
high-performance, scalable applications.  

> 📖 Learn more about **[Redis Cluster Operator](https://redis.io/tutorials/operate/orchestration/kubernetes-operator/)**.

## Registration
This deployment provisions a Redis cluster in Kubernetes.  

Before running the GitHub Action, add the following GitHub organization secrets.  

| Secret                                  | Type    | Description                                     |
| --------------------------------------- | ------- | ----------------------------------------------- |
| `{{environment}}_REDIS_ADMIN_USERNAME`  | secrets | The username of the primary Redis admin user.   |
| `{{environment}}_REDIS_ADMIN_PASSWORD`  | secrets | The password of the primary Redis admin user.   |

To access the Redis cluster locally, use port-forwarding to expose it by running the following command.

```powershell
kubectl port-forward redis-cluster-0 6379 -n $env:KUBERNETES_NAMESPACE;
```

To retrieve the deployed Redis cluster from the Custom Resource Definition (CRD), run.  

```powershell
kubectl get rabbitmqclusters -n {{namespace}};
```

## Dependencies
Redis has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                            | Description                          | 
| ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**  | The Azure Kubernetes Service (AKS).  |
