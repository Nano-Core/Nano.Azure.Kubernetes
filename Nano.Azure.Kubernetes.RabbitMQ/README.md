# Nano.Azure.Kubernetes.RabbitMQ
[![Build and Deploy](https://github.com/Nano-Core/Nano.Azure.Kubernetes.RabbitMQ/actions/workflows/build-and-deploy.yml/badge.svg)](https://github.com/Nano-Core/Nano.Azure.Kubernetes.RabbitMQ/actions/workflows/build-and-deploy.yml)  
_RabbitMQ is an open-source message broker software that facilitates communication between applications by allowing them to send and receive messages asynchronously. It uses a queue-based architecture, where messages are published to queues and then consumed by one or more consumers. RabbitMQ supports various messaging protocols, including AMQP (Advanced Message Queuing Protocol), making it highly flexible and suitable for a wide range of use cases. It's widely used for building scalable, distributed systems and microservices due to its reliability, fault tolerance, and ability to handle large volumes of messages efficiently._  

***

### Deployment

#### Commands
* ```kubectl port-forward rabbitmq-0 15672```
 
*** 

### Dependencies
* [Nano.Azure.Kubernetes](https://github.com/Nano-Core/Nano.Azure/tree/master/Nano.Azure.Kubernetes)

*** 

### References
* https://rabbitmq.com
* https://bitnami.com/stack/rabbitmq/helm
* https://artifacthub.io/packages/helm/bitnami/rabbitmq

***

  RABBITMQ_ADMIN_USERNAME: rabbitmq_user
  RABBITMQ_ADMIN_PASSWORD: ${{ github.ref == 'refs/heads/master' && vars.PRODUCTION_RABBITMQ_ADMIN_PASSWORD || vars.STAGING_RABBITMQ_ADMIN_PASSWORD }}
  RABBITMQ_ERLANG_COOKIE: ${{ github.ref == 'refs/heads/master' && vars.PRODUCTION_RABBITMQ_ERLANG_COOKIE || vars.STAGING_RABBITMQ_ERLANG_COOKIE }}

