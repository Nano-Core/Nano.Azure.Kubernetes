# Nano.Azure.Kubernetes.Ollama

> Ollama deployment for Nano AI model hosting._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[Ollama Configuruation](#ollama-configuruation)**  
  * **[High Availability](#topology-affinity)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
  * **[Horizontal Pod Autoscaler](#horizontal-pod-autoscaler)**  
  * **[GPU Nodepool](#gpu-nodepool)**  
* **[Dependencies](#dependencies)**  

## Summary
Ollama allows the users to run open-source large language models, such as Llama 2, locally. Ollama bundles model weights, configuration, and data into a single package, defined by a Modelfile.

> 📖 Learn more about **[Ollama](https://ollama.com)**.
> 📖 Learn more about **[Ollama API](https://github.com/ollama/ollama/blob/main/docs/api.md)**.
> 📖 Learn more about **[Ollama Library](https://ollama.com/library)**.

## Registration
This deployment provisions Ollama in Kubernetes.  

The deployment uses the [Ollama Helm Chart](https://artifacthub.io/packages/helm/ollama-helm/ollama) to provision and manage the underlying infrastructure required for Ollama.  

To access Ollama locally, use port-forwarding to expose the pod by running the following command.

```powershell
kubectl port-forward $env:APP_NAME 11434 -n $env:KUBERNETES_NAMESPACE;
```

### Ollama Configuration
GPU support is enabled by default, and the deployment is designed to run on NVIDIA GPU nodes using Kubernetes GPU resource scheduling. This ensures inference workloads can take advantage of 
hardware acceleration when available.

Model management is fully declarative. Models can be defined for both pulling and loading into memory, giving full control over which models are available at runtime. Any models removed from 
the configuration will be deleted during deployment to keep the environment aligned with the declared state.  By default, no models are configured. It is therefore required to explicitly define 
which models should be pulled or loaded, and to ensure that sufficient memory and GPU resources are allocated for the selected models.

### High Availability
The Ollama deployment is configured with Kubernetes pod anti-affinity rules to encourage replicas to be scheduled across different cluster nodes. This helps improve workload availability 
and resilience by reducing the risk of multiple Ollama pods being affected by a single node failure. The affinity configuration uses the Kubernetes hostname topology key to distribute 
pods across the cluster whenever possible.  

### Hardened Security
The security context is hardened for production use. Privilege escalation is disabled, and the container is explicitly prevented from running as root (`runAsNonRoot: true`). All Linux 
capabilities are dropped to minimize the attack surface, and the filesystem is set to read-only to prevent any runtime modifications.

The container runs with a dedicated non-root user to enforce least-privilege access to mounted volumes. A runtime-default seccomp profile is applied to restrict system calls and further 
reduce exposure to kernel-level risks.  

### Prometheus Monitoring
Ollama is integrated with Prometheus monitoring in Azure Kubernetes Service (AKS) using a `ServiceMonitor`, because its metrics are exposed through stable Service endpoints that 
ensure reliable scraping and consistent observability of the StatefulSet pods across restarts, rescheduling, and scaling events.

> ⚠️ Azure Prometheus uses different CRDs: `azmonitoring.coreos.com/v1` instead of `monitoring.coreos.com/v1`.

### Health Probes
The deployment configures startup, readiness, and liveness probes. These are intentionally set with conservative thresholds to allow sufficient time for cluster stabilization and quorum 
leader re-election during startup or failover scenarios.

### Horizontal Pod Autoscaler
Horizontal Pod Autoscaling (HPA) is not enabled for Ollama deployments by default.

Ollama workloads are typically constrained by memory and GPU resources rather than CPU utilization. Because each replica must load one or more large language models into memory, reactive 
scaling based on CPU or memory metrics can lead to inefficient resource usage and frequent model loading overhead.

Instead of automatic scaling, capacity is managed explicitly by controlling the number of replicas. This ensures predictable performance, avoids unnecessary model reloads, and provides stable 
latency characteristics for inference workloads.  

Autoscaling may be enabled by adding the following configuration to `ollama-values.yaml`.

```yaml
autoscaling:
  enabled: true
  minReplicas: {{min-replica}}
  maxReplicas: {{max-replica}}
  targetCPUUtilizationPercentage: 180
  targetMemoryUtilizationPercentage: 180
````

### GPU Nodepool
This deployment requires a pre-provisioned GPU node pool in the Kubernetes cluster, as workloads are intended to run on GPU-enabled nodes for hardware-accelerated inference.

The nodepool must be created in advance, as described in the guide for [setting up a GPU cluster in AKS](https://learn.microsoft.com/en-us/azure/aks/gpu-cluster), to ensure GPU-capable nodes 
are available for scheduling workloads. GPU support must be enabled using the NVIDIA GPU Operator, which installs and manages drivers, device plugins, and runtime components required for 
Kubernetes to schedule and run GPU workloads, as described in the [NVIDIA GPU Operator getting started guide](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html).

Without this prerequisite, GPU workloads in this deployment will not be scheduled successfully.  

## Dependencies
Ollama has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                                                | Description                                  | 
| --------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------- | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**                      | The Azure Kubernetes Service (AKS).          |
| **[Nano.Azure.GitHubRunner](https://github.com/Nano-Core/Nano.Azure.GitHubRunner/tree/master/Nano.Azure.GitHubRunner/README.md#nanoazuregithubrunner)**   | The GitHub Runner container job deployment.  |
