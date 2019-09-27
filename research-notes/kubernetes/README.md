# Kubernetes

## Q&A

- Would you combine more than 1 container in a pod as a best practice?
- What's the difference between kubectl create and kubectl apply
    - Apply allows you to make changes to an existing YAML file. It also acts as create, so you can use apply by default
- How do we auto scale a pod via a deployment, instead of manually scaling to x replicas
- How do we prioritize Pods, so that they load based on a dependency tree (e.g. MongoDB before API Server)
- How to we check if a pod is alive or crashed


## Notes

- Kubernetes is an open source orchestration system for Docker Containers
    - Lets you schedule containers on a cluster of machines
    - You can run multiple containers on 1 machine
    - Manages the state of containers
        - Can start the container on specific nodes
        - Will restart a container when it gets killed
        - Can move containes from one node to another node
            - Good when you need to do maintenance on a specific node
- Alternatives to Kubernetes are:
    - Docker Swarm
    - Mesos
- Kubernetes can run on public and private clouds as a hybrid
    - Some workloads might be kept on-premise (private) and others in the cloud (public)
    - Same system with same abstraction on both environments
- Kubernetes is highly modular, open source, great community and backed by Google
- Things like Volumes and External Load Balancers work only with supported Cloud Provders
- Google's been running Containerized clusters for more than 10 years using they system "Borg"
- A pod can contain 1 or more tightly coupled containers, that make up the app
    - They would easily communicate with each other using their port numbers
- Busybox seems to be a pod that allows you to run shell commands on the kubernetes cluster
- In a real world scenario, you need to be able to access your apps from outside the cluster. This is achieved using Load Balancers, which are available via cloud providers
- Load Balancers route the traffic to the correct pod in Kubernetes
- Alternative load balancers that can exist in front of your cluster are:
    - haproxy
    - nginx
- If your application is stateless, you can horizontally scale it
    - Stateless: Your application doesn't have a state, it doesn't write any local files or keep local sessions
- Most Web applications can be made stateless:
    - Session management needs to be done outside the container
    - Any files that need tobe saved can't be saved locally on the container
    - Memcache, Redis or a database can be used for session management
    - Object Storage needs to be used for storing files
- Scaling in Kubernetes is done using the Replication Controller
    - It ensures a specified number of pod replicas will run all the time
- Pods created with the replica controller will automatically be replaced if they fail, get deleted or are terminated
- Replica Set is the next generation Replication Controller
    - It supports a new selector that can do selection based on filtering according to a set of values
        - e.g. "environment" either "dev" or "qa"
        - not only based on equality, like the Replication Controller
- Replica Set is used by the Deployment Object    
- A Deployment is a declaration in Kubernetes that allows you to do app deployments and updates
    - You define the state of your application
    - Kubernetes then ensures the clusters matches the state of your deployment
- With a Deployment you can:
    - Create a deployment (e.g. deploy an app)
    - Update a deployment (e.g. deploy a new version of your app)
    - Do rolling updates (zero downtime deployments)
    - Rollback to a previous version
    - Pause/resume a deployment
- Pods are very dynamic, they come and go on the Kubernetes cluster
    - When using a Replication Controller, pods are terminated and created during scaling operations
    - When using deployments, when updating the image version, pods are terminated and new pods take the place of older pods
    - Pods should never be accessed directly, but always through a service
- A service is the logical bridge between the "mortal" pods and other services or end users
- Service Types that can be defined in Service profile are:
    - ClusterIP: (default) (A virtual IP Address only reachable from within the cluster)
    - NodePort: (A port that is the same on each node that is also reachable externally)
    - LoadBalancer: (Created by the cloud provider that will route external traffic to every node on the NodePort)
- By default, services can only run between ports 30000-32767, but you could change this behaviour by adding the "--service-node-port-range=" argument to the kube-apiserver (in the init scripts)    
- Labels are key/value pairs that can be attached to objects
    - Labels are like tags
    - You can label your objects, e.g. your pod, following an org structure
        - e.g. Key=environment - Value=dev/staging/qa/prod
        - e.g. Key=department - Value=engineering/finance/marketing
    - Labels are no unique and multiple labels can be added to one object
    - Once labels are attached to objects, you can use filters to narrow down results
        - This is called label selectors
        - Using label selectors, you can use matching expressions to match labels
        - e.g. a particular pod can only run on a node labeled with environment="development"
    - To tag a node:
        - kubectl label nodes node1 key=value
        - Then, in the Pod definition, you will add:
            nodeSelector:
                key=value
- If your app malfunctions, the pod and container might still be running, but the application might not work anymore
    - Use Health Checks to detect and resolve problems with your application
    - There are 2 types of health checks:
        - Running a command in the container periodically
        - Periodic checks on a URL (HTTP)
    - In Pod Definition, add the following:
        livenessProbe:
            httpGet:
                path: /
                port: portnumber
            initialDelaySeconds: 15
            timeoutSeconds: 30
- Whilst a livenessProbe indicates whether a container is running, a readinessProbe indicates whether a container is ready to serve requests
    - If the check fails, the container will not be restarted, but the Pod's IP address will be removed from the Service, so it'll not serve any requests anymore
- Secrets provides a way in Kubernetes to distribute credentials, keys, passwords or "secret" data to the pods
    - Kuberentes itself uses this Secrets mechanism to provide the credentials to access the internal API
    - You can also use the same mechanism to provide secrets to your application
    - Secrets can be used in the following ways:
        - Use secrets as environment variables
        - As a file in a pod
            - This uses volumes
        - An external images to pull secrets (from a private registry)
- The DNS service can be used within pods to find other services running on the same cluster
    - Multiple containers within 1 pod don't need this service, as they can contact each other directly
        - A container in the same pod can connect to the port of the other container directly using localhost:port
    - To make DNS work, a pod will need a service definition
    - Pods and services can be launched in different namespaces (to logically separate your cluster)
    - To access a service in a namespace:
        host servicename.namespace
    - To acccess a service using the full hostname:
        host servicename.default.svc.cluster.local
- Configuration parameters that are not secret, can be put in a ConfigMap
    - The input is key/value pairs
    - kubectl create configmap name --from-file=config.json
- Ingress is a solution that allows inbound connections to the cluster
    - It's an alternative to the external Loadbalancer and nodePorts
    - Ingress allows you to easily expose services that need to be accessible from outside to the cluster
    - With ingress you can run your own ingress controller (basically a loadbalancer) within the Kubernetes cluster
    - There are default ingress controllers available, or you can write your own
    - You can use 1 LoadBalancer that captures all the external traffic and send it to the ingress controller
    - The ingress controller can be4 configured to route the different traffic to all your apps based on HTTP rules (host and prefixes)
    - The only works for HTTP(s)-based applications
- External DNS will automatically create the necessary DNS records in your external DNS server
    - For every hostname that you use in ingress, it'll creat a new record to send traffic to your loadbalancer
    - Major DNS providers are supported:
        - Google Cloud DNS
        - Route53
        - AzureDNS
        - CloudFlare
        - DigitalOcean
        - etc.
- Volumes in Kubernetes allow you to store data outside of containers
    - When a container stops, all data on the conttainer itself is lost
    - Persistent Volumes in Kubernetes allow you to attach a volume to a conteiner that will exist even when the container stops
    - Volumes can be attached using different plugins:
        - Local Volume
        - EBS Storage (AWS Cloud)
        - Google Disk
        - Azure Disk (Microsoft)
        - NFS or Cephfs (Network Storage)
- The Kubernetes plugins have the capability to provision storage for you
    - There are cloud provider plugins that auto create volumes before attaching them to nodes
    - This is done using the StorageClass object
- Pod Presets can inject information into pods at runtime
    - Are used to inject Kubernetes resources like Secrets, ConfigMaps, Volumes and Environment variables
    - Imagine having 20 applications, who all need a specific credential
        - You could edit the 20 specifications and add the credential, or
        - You can use presets to create 1 Preset object, which will inject an environment variable or confir file to all matching pods
    - When injecting Environment variables and VolumeMounts, the Pod Preset will apply the changes to all containers within the pod
    - You can use more than 1 pod preset, they'll be applied to all matching pods
    - If there's a conflict, the PodPreset will not be applied to the pod
    - PodPresets can match zero or more pods
        - It's possible that no pods are currently matching, but that matching pods will be launched at a later time
- StatefulSets are used to run stateful applications
    - i.e. That need a stable pod hostname (instead of a podname-randomstring)
        - Your pod name will have a sticky identity, using an index,m e.g. podname-0, podname-1, etc. (and when a pod fget re-scheduled, it'll keep that identity)
    - StatefulSets allow stateful apps stable storage with volumes based on their ordinal number (podname-x)
    - A StatefulSet will allow your stateful app to use DNS to find other peers
    - A StatefulSet will also allow your stateful app to ortder the starttup and teardown
- Daemon Sets ensure that every single node in the Kubernetes cluster runs the same pod resource
    - This is useful if you want to ensure that a certain pod is running on every signle kubernetes node
    - When a node is added to the cluster, a new pod will be started automatically
    - Same when a node is removed, the pod will not be rescheduled on another node
    - Typical use cases:
        - Logging Aggregators
        - Monitoring
        - Load Balancers / Reverse Proxies / API Gateways
- Heapster enables Container Cluster Monitoring and Performance Analysis
    - It's providing a monitoring platform for Kubernetes
    - It's a prerequisite if you want to do pod auto-scaling in Kubernetes
    - Heapster exports clusters metrics via REST endpoints
    - You can use different backends with Heapster
        - InfluxDB
        - Google Cloud Monitoring/Logging
        - Kafka
    - Visualizations (graphs) can be shown using Grafana
    - The Kubernetes dashboard will also show graphs once monitoring is enabled
    - All these technologies can be started in pods
    - NOTE: As of Kubernetes 1.8+, Heapster is no longer required and is replaced by Metrics Server
        - You cannot extract metrics manually, so you'll need a 3rd party tool like Prometheus
- Kubernetes has the possibility to automatically sclae pods based on metrics
    - Kubernetes can automatically sclae a deployment, Replication cController or ReplicaSet
    - Scaling can be based on CPU usage, queries per second, average request latency, etc.
    - Autoscaling will periodically query the utilization for the targeted pods
        - By default 30 seconds
    - Example:
        - You run a deployment with a pod with a CPU Resource request of 200m
            - 200m = 200 millicpu (or also 200 millicores)
            - 200m - 0.2, which is 20% of a CPU core of the running node
                - If the node has 2 cores, it's still 20% of a single core
        - You introduce autoscaling at 50% of the CPU usage (which is 100m)
        - Horizontal Pod Autoscaling will increase/decrease pods to maintain a target CPU utilization of 50% (or 100m / 10% of a core within this pod)
- The Affinity/anti-affinity feature allows you to do more complex scheduling than the nodeSelector and also works on pods
    - The language is more expressive
    - You can create rules that are not hard requirements, but rather a preferred rule, meaning that the scheduler will still be able to schedule your pod, even if the rules cannot be met
    - You can create rules that take other pod labels into account
        - e.g. a rule that makes sure 2 different pods will never be on the same node
    - Kubernetes can do node affinity and pod affinity/anti-affinity
    - Node affinity is similar to nodeSelector
    - Pod affinity/anti-affinity allows you to create rules how pods should be scheduled taking into account other running pods
    - Affinity/anti-affinity mechanism is only relevant during scheduling, once a pod is running, it'll need to be recreated to apply the rules again
    - There are 2 types that can be used for node affinity:
        - requiredDuringSchedulingIgnoredDuringExecution
        - preferredDuringSchedulingIgnoredDuringExecution
    - You can also supply a weighting to the preferred...Execution
        - The higher the weighting, the more weight is given to that rule
        - When scheduling, Kubernetes will score every node by summarizing the weightings per node
    - In addition to the labels that you can add yourself to nodes, there are pre-populated labels that you can use:
        - kubernetes.io/hostname
        - failure-domain.beta.kubernetes.io/zone
        - failure-domain.beta.kubernetes.io/region
        - beta.kubernetes.io/instance-type
        - beta.kubernetes.io/os
        - beta.kubernetes.io/arch
- Interpod affinity/anti-affinity allows you to influence scheduling based on the labgels of ther pods that are already running on the cluster
    - Pods belong to a namespace, so your affinity rules will apply to a specific namespace (if no namespace is given in the specification, it defaults to the namespace of the pod)
    - There are 2 types that can be used for pod affinity:
        - requiredDuringSchedulingIgnoredDuringExecution
        - preferredDuringSchedulingIgnoredDuringExecution   
    - A good use case for pod affinity is co-located pods
        - You might want that 1 pod is always co-located on the same node with another pod
        - e.g. you hve an app that uses redis as cache, and you want to have the redis pod on the same node as the app itself
    - Another use case is to co-locate pods within the same availability zone 
    - Pod anti-affinity allows you to make sure a pod is only scheduled once on a node
    - The following operators can be used:
        - In, NotIn (does a label have one of the value)
        - Exists, DoesNotExist (does a label exist or not)
    - Interpod affinity and anti-affinity currently requires a substantial amount of processing
- Tolerations is the opposite of node affinity, as in it allows a node to repel a set of pods
    - Taints mark a node, tolerations are applied to pods to influence the scheduling of the pods
- Custom Resource Definitions lets you extend the Kubernetes API
    - Resources are the endpoints in the Kubernetes API that store collections of API objects
    - A custom resource is a resource that you might add to your cluster, it's not available on every cluster
    - It's an extension of the Kubernetes API
    - Custom Resources are also described in YAML files
    - Ad an admin, you can dynamically add CRDs to add extra functionality to your cluster
    - Operators use CRDs to extend the Kubernetes API with their own functionality
- An Operator is a method of packaging, deplying and managing a Kubernetes application
    - It puts operational knowledge in an application
    - It brings the user closer to the experience of managed cloud ser ices, rather than having to know all the specific of an application deployed to Kubernetes
    - Once an operator is deployed, it can be managed using CRDs
    - It also provides a great way to deploy Stateful services on Kubernetes
- When a Kubernetes cluster is uxsed by multiple people or teams, resource management becomes more important
    - You want to be able to manage the resrouces you give to a person or a team
    - You don't want one person or team taking up all the resources (e.g. CPU/Memory) of the cluster
    - You can divide your cluster into namespaces and enable resource quotas in it
        - You do this using the ResourceQuota and ObjectQuote objects
    - Each container can specify request capacity and capacity limits
    - Request capacity is an explicit request for resources
        - The scheduler can use the request capacity to make decisions on where to put the pod on
        - You can see it as a minimum amount of resources the pod needs
    - Resource limit is a limit imposed to the container
        - The container will not be able to utilize more resources than specified
- Namespaces allow you to create virtual clusters within the same physical cluster
    - Namespaces logically separate your cluster
    - The std namespace is called "default" and that's where all resources are launched by default
        - There is also namespace for kubernetes specific resources, called "kube-system"
    - Namespaces are intended when you have multiple teams / projects using the Kubernetes cluster
    - The name of resources need to be uniquew within a namespace, but not across namespaces
    - You can divide resources of a Kubernetes cluster using namespaces
- There are 2 types of users you can create:
    - A normal user, which is used to access the cluster externally
        - e.g. through kubectl
        - This user is not managed using objects
    - A service user, which is managed by an object in Kubernetes
        - This type of user is usecd to authenticate within the cluster
        - e.g. from inside a pod, or from a kubelet
        - These credentials are managed like Secrets
    - There are multiple authentication strategies for normal users:
        - Client Certificates
        - Bearer tokens
        - Authentication Proxy
        - HTTP Basic Authentication
        - OpenID
        - Webhooks
    - Service users are using Service Account Tokens
        - They are stored as credentials using Secrets
            - Those secrets are also mounted into pods to allow communication between the services
        - Service users are specific to a namespace
        - They are created automatically by the API or manually using Objects
        - Any API call not authenicated is considered as an anonymous user
    - Independently from the authentication mechanism, normal users have the following attributes:
        - Username
        - UID
        - Groups
        - Extra fields to store information
    - After a normal user authenticates, it will have access to everything
        - To limit access, you need to configure authorization
        - There are multiple offerings to choose from:
            - AlwaysAllow / AlwaysDeny
            - ABAC (Attribute-Based Access Control)
            - RBAC (Role-Based Access Control)
            - Webhook (Authorization by Remote Service)
                - You can parse the incoming payload (which is JSON) and reply with access granted or access denied
- RBAC
    - After Authentication, Authorization controls what the user can do, what does the user have access to
    - The access controls are implemented on an API level (kube-apiserver)
    - When an API request comes in (e.g. when you enter kubectl get nodes), it will be checked to see whether you have access to execute this command
    - RBAC regulates access using roles
        - Allows admins to dynamically configure permission policies 
    - You add RBAC resources with kubectl, describing them first in YAML format, then applying them to the cluster
    - First you define a role, then you can assign users/groups to that role
    - You can create roles limited to a namespace or can you create roles where the access applies to all namespaces
        - Role (single namespace) and ClusterRole (cluster-wide)
        - RoleBinding (single namespace) and ClusterRoleBinding (cluster-wide)
- Networking
    - In Kubernetes, the pod itself should always be routable
    - This is Pod-to-Pod communication
    - Kubernetes assumes that pods should be able to communicate to other pods, regardless of which node they are running
        - Every pod has its own IP Address
        - Pods on different nodes need to be able to communciate to each other using those ip addresses
            - This is implemented differently depending on your kubernetes setup
- Node Maintenance
    - It's the Node Controller that is responsible for manging the Node Objects
    - It assigns IP space to the node when a new node is launched
    - It keeps the node list up to date with the available machines
    - the node controller is also nomitoring the health of the node
        - If a node is unhealthy it gets deleted
        - Pods running on the unhealthy node will then get re-scheduled
    - When adding a new node, the kubelet will attempt to register itself
        - This is called self registration and is the default behaviour
    - It allows you to easily add more nodes to the cluster without making API changes yourself
    - A new node object is automatically created with:
        - The metadata (with a name: IP or hostname)
        - Labels (e.g. cloud region / availability zone / instance size)
    - A node also has a node condition (e.g. Ready / OutOfDisk)
    - When you wan to decommission a node, you want to do it gracefully
        - You drain a node before you shut it down or take it out of the cluster
    - To drain a node:
        kubectl drain nodename --grace-period=600
    - If the node runs pods not managed by a controller, but is just a single pod
        kubectl drain nodename --force
- High Availability
    - If you're going to run your cluster in production, you're going to want to have all your master services in a high availability (HA) setup, which is as follows:
        - Clustering etcd: at least run 3 etcd nodes
        - Replicated API Servers with a Load Balancer
        - Running multiple instances of the scheduler and the controllers
            - Only one of them will be the leader, the other ones are on stand-by
- Helm
    - Helm is the best way to find, share and use software built for kubernetes
    - You run helm client on your machine, but install Tiller on the Kubernetes cluster
        helm init
    - If you have RBAC installed, you'll also need to add a ServiceAccount and RBAC rules
    - Helm uses a package format called charts
        - A chart is a collection of files that describe a set of Kubernetes resources
        - A single chart can deploy an app, a piece of software or a database
        - It can have dependencies, e.g. to install wordpress chart, you need mysql chart
        - You can write you own chart to deploy your application on Kubernetes using Helm
    - Charts uses templates that are typically developed by a package maintainer
        - They will generate YAML files that Kubernetes understands
        - You can think of templates as dynamic YAML files, which can contain logic and variables
    - Helm Commands
        - helm init (Install tiller on the cluster)
        - helm reset (Remove tiller from the cluster)
        - helm install (install a helm chart)
        - helm search (search for a chart)
        - helm list (list releases - installed charts)
        - helm upgrade (upgrade a release)
        - helm rollback (rollback a release to the previous version)
    - To create your own helm chart:
        - helm create mychart
- Serverless        
    
## TO DOS

- Become familiar with kubectl commands for pods, allowing you to interact with pods and debug, troubleshoot if necessary
- Find a full-proof solution for volumes across multiple notes on Azure
- Learn how to use Prometheus and Grafana for Monitoring and Logging
- Learn what are the best practices for autoscaling (i.e. what conditions should be used to determine when to scale up/down)


## Examples of Useful Commands

- kubectl create namespace myspace
- kubectl get namespaces
- kubectl top node (Displays metrics of all nodes)
- kubectl top pod (Displays metrics of pods)
- kubectl create -f filename.yml
- kubectl rollout status deployment/name (Get deployment status)
- kubectl rollout history deployment/name (Get rollout history)
- kubectl rollout undo deployment/name (Rollback to previous version)
- kubectl rollout undo deployment/name --to-revision=name (Rollback to any version)
- kubectl edit deployment/name
- kubectl create configmap name --from-file=config.json

Pod Commands
- kubectl get pod (Get info about all running pods)
- kubectl describe pod podname (Describe one pod)
- kubectl expose pod podname --port=portnumber --name=servicename
- kubectl attach podname -i
- kubectl exec podname --ls /app
- kubectl port-forward podname port:containerport
- kubectl run -i --tty busybox --image=busybox --restart=Never -- sh (Runs a shell in a pod - very useful for debugging)
- kubectl get deployments
- kubectl get rs
- kubectl get pods --show-labels

## Buzz Words & Terms
- 

## What is machine learning?

- 

## Resource Links

- [Udemy: Learn DevOps: The Complete Kubernetes Course](https://www.udemy.com/learn-devops-the-complete-kubernetes-course)
    - [Course Procedure Document & Links](https://www.udemy.com/learn-devops-the-complete-kubernetes-course/learn/lecture/6018348#overview)
    - [Course Material on GitHub](https://github.com/wardviaene/kubernetes-course)
    - [Course Slides](https://d3jb1lt6v0nddd.cloudfront.net/udemy/Learn+DevOps+-+Kubernetes.pdf)
    - [Facebook Group](https://www.facebook.com/groups/840062592768074/)
- [12-Factor App](https://12factor.net)