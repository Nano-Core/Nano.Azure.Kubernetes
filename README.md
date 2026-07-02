# Nano.Azure.Kubernetes

> _Cloud-native Kubernetes workloads, Helm-based deployments, and platform components for Nano applications._

***

## Table of Contents
&nbsp;&nbsp;&nbsp;&nbsp;📌 **[Summary](#-summary)**  
&nbsp;&nbsp;&nbsp;&nbsp;⚙️ **[Required Tools](#-required-tools)**  
&nbsp;&nbsp;&nbsp;&nbsp;⚖️ **[Licenses](#-licenses)**  

### Documentaion
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.CertManager](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.CertManager/README.md#nanoazurekubernetescertmanager)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.ClamAV](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.ClamAV/README.md#nanoazurekubernetesclamav)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.Firebase](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.RabbitMq/README.md#nanoazurekubernetesfirebase)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.Gateway](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.Gateway/README.md#nanoazurekubernetesgateway)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.Grafana](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.RabbitMq/README.md#nanoazurekubernetesgrafana)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.Ollama](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.RabbitMq/README.md#nanoazurekubernetesollama)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.RabbitMq](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.RabbitMq/README.md#nanoazurekubernetesrabbitmq)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.Redis](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.RabbitMq/README.md#nanoazurekubernetesredis)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.SendGrid](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.RabbitMq/README.md#nanoazurekubernetessendgrid)**  
&nbsp;&nbsp;&nbsp;&nbsp;🔹 **[Nano.Azure.Kubernetes.Twilio](https://github.com/Nano-Core/Nano.Azure.Kubernetes/tree/master/Nano.Azure.Kubernetes.RabbitMq/README.md#nanoazurekubernetestwilio)**  

## 📌 Summary
Nano.Azure.Kubernetes provides a curated set of Kubernetes deployments, Helm charts, and platform components designed to support **[Nano Applications](https://github.com/Nano-Core/Nano.Library/blob/master/README.md#nanolibrary)**. 
The goal is to standardize how Nano applications are deployed and operated in Kubernetes by providing.  

- Pre-configured deployment patterns
- Production-ready Helm-based infrastructure components
- Reusable and composable Kubernetes building blocks
- Opinionated but flexible cluster setups

This repository focuses on leveraging Kubernetes-native primitives to enable consistent and scalable deployments of Nano applications on Azure managed Kubernetes services.

The key principles in the Nano Kubernetes infrastructure are.  

- Kubernetes-native design – prefer native Kubernetes resources and patterns  
- Consistency over configuration – standardized deployments across all Nano applications  
- Infrastructure as code – everything is reproducible and version-controlled  
- Minimal operational overhead – reduce complexity in deployment and operations  
- Composable architecture – components can be combined or deployed independently  

#### Azure Architecture
![Nano Kubernetes Architecture](https://raw.githubusercontent.com/Nano-Core/Nano.Azure.Kubernetes/v10.0.0-ga/.assets/Nano-Kubernetes.jpg)

## ⚙️ Required Tools
Before continuing, make sure you have the following tools installed and configured.

| Tool                                                               | Description                                                                                                                      |
| ------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| **[Kubectl CLI](https://kubernetes.io/docs/reference/kubectl/)**   | Kubernetes provides a command line tool for communicating with a Kubernetes cluster's control plane, using the Kubernetes API.   |

> ⚠️ If Kubectl is already installed, make sure it is updated to the latest version.

## ⚖️ Licenses
Nano is free to use and released under the MIT License.  

The deployments in this repository install and configure third-party software, which is licensed under their respective upstream open-source licenses.  

| License                                                 | Description                                                                     |
| ------------------------------------------------------- | ------------------------------------------------------------------------------- |
| [Apache-2.0](https://licenses.nuget.org/Apache-2.0)     | Permissive with patent protection.                                              |
| [MIT](https://opensource.org/license/MIT)               | Permissive license with minimal restrictions.                                   |
| [MPL-2.0](https://opensource.org/licenses/MPL-2.0)      | Weak copyleft license allowing use, modification, and source sharing.           |
