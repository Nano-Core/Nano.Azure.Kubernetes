# Nano.Azure.Kubernetes.ClamAV

> _Pluggable ClamAV file anti-virus scanner for Nano web-based applications._

***

## Table of Contents
* **[Summary](#summary)**  
* **[Registration](#registration)**  
  * **[Topology Affinity](#topology-affinity)**  
  * **[Hardened Security](#hardened-security)**  
  * **[Prometheus Monitoring](#prometheus-monitoring)**  
  * **[Health Probes](#health-probes)**  
  * **[Horizontal Pod Autoscaler](#horizontal-pod-autoscaler)**  
* **[Dependencies](#dependencies)**  

## Summary
ClamAV is an open-source antivirus toolkit designed to detect and remove malware, viruses, and other malicious threats from files and systems. It provides a command-line scanner and a 
daemon for real-time scanning, offering comprehensive protection for a variety of operating systems. ClamAV uses a regularly updated virus definition database to identify threats, and 
it supports multiple file formats, including compressed and archived files. Its integration capabilities with other security tools and systems make it a versatile choice for enhancing 
cybersecurity. Widely used in both personal and enterprise environments, ClamAV is valued for its effectiveness and the support of a strong open-source community.

> 📖 Learn more about **[ClamAV](https://docs.clamav.net)** or the [ClamAV Helm Chart](https://artifacthub.io/packages/helm/wiremind/clamav).

## Registration
This deployment provisions ClamAV in AKS.  

To connect to your ClamAV instance from outside the cluster execute the following commands.

```powershell
kubectl port-forward clamav-0 3310:3310
```

COMMANDS FOR HELM 
uninstall
list

### Topology Affinity

### Hardened Security
By defaault the ClamAV Helm chart is hardened and secure. No overrides needed

### Prometheus Monitoring
Monitoring enabled for prometheus and exposes `/metrics`.

### Health Probes
By defaault the ClamAV Helm chart is configured with startup, readiness and liviness probe. No overrides needed

### Horizontal Pod Autoscaler
Configured.

## Dependencies
RabbitMQ has the following dependencies that must be deployed or otherwise satisfied prior to setup.  

| Dependency                                                                                                                            | Description                          | 
| ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ | 
| **[Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes/README.md#nanoazurekubernetes)**  | The Azure Kubernetes Service (AKS).  |
