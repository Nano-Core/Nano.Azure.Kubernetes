# Nano.Azure.Kubernetes.Ollama
_Ollama allows the users to run open-source large language models, such as Llama 2, locally. Ollama bundles model weights, configuration, and data into a single package, defined by a Modelfile._  

***

### Deployment

#### Commands
* ```kubectl port-forward ollama-xxxxxxxxx-xxxx 11434 --namespace ollama```
 
*** 

### Dependencies
* [Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes)

*** 

### References
* https://github.com/ollama/ollama
* https://github.com/ollama/ollama/blob/main/docs/api.md
* https://artifacthub.io/packages/helm/ollama-helm/ollama
* https://ollama.com/library

GPU:
* https://learn.microsoft.com/en-us/azure/aks/gpu-cluster
* https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html

***
