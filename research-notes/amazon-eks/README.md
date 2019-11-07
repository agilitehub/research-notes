# Amazon EKS (Elastic Kubernetes Service)

## Table of Contents
- [What is EKS](#what-is-eks)
- [Benefits of EKS](#benefits-of-eks)
- [Related Links](#related-links)

## What is EKS

Amazon Elastic Kubernetes Service (Amazon EKS) makes it easy to deploy, manage, and scale containerized applications using Kubernetes on AWS.

Amazon EKS runs the Kubernetes management infrastructure for you across multiple AWS availability zones to eliminate a single point of failure. Amazon EKS is certified Kubernetes conformant so you can use existing tooling and plugins from partners and the Kubernetes community. Applications running on any standard Kubernetes environment are fully compatible and can be easily migrated to Amazon EKS.

Amazon EKS supports both Windows Containers and Linux Containers to enable all your use cases and workloads.

## Benefits of EKS

### No control plane to manage

Amazon EKS runs the Kubernetes management infrastructure across multiple AWS Availability Zones, automatically detects and replaces unhealthy control plane nodes, and provides on-demand upgrades and patching. You simply provision worker nodes and connect them to the provided Amazon EKS endpoint.

### Secure by default

Secure and encrypted communication channels are automatically setup between your worker nodes and the managed control plane, making your infrastructure running on Amazon EKS secure by default. 

### Built with the community

AWS actively works with the Kubernetes community, including making contributions to the Kubernetes code base that help Amazon EKS users take advantage of AWS services and features.

### Conformant and compatible

Amazon EKS runs upstream Kubernetes and is certified Kubernetes conformant, so applications managed by Amazon EKS are fully compatible with applications managed by any standard Kubernetes environment. 

### Optimized for cost

Run containers on Amazon EC2 Spot Instances to receive up to a 90% discount compared to On-Demand prices. Containers are often stateless and fault-tolerant, making them a great fit for Spot Instances. You can easily run EKS clusters at scale by mixing Spot Instances with On-Demand and Reserved Instances.

## Related Links

- [Amazon EKS Website](https://aws.amazon.com/eks)