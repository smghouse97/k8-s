# k8-s
Kubernetes Repository 
# Kubernetes

Kubernetes is an open-source container orchestration platform developed by Google. It is used to automate the deployment, scaling, and management of containerized applications.

Kubernetes is designed to work with different containerization tools like Docker, rkt, and CRI-O. It provides a platform for managing the complete lifecycle of containerized applications from deployment to scaling and monitoring.

Kubernetes provides a variety of features that make it an ideal choice for managing containerized applications. Some of these features include:

- **Automated deployment and scaling**: Kubernetes allows you to deploy and scale your applications automatically based on the demand.
- **Service discovery and load balancing**: Kubernetes enables service discovery and load balancing among the containers in a cluster.
- **Self-healing**: Kubernetes ensures that containers are always running by automatically restarting failed containers.
- **Rolling updates and rollbacks**: Kubernetes provides a way to update and roll back containerized applications without downtime.
- **Resource management**: Kubernetes allows you to manage the resources used by your applications by setting resource constraints and limits.

Kubernetes has a large and active community of developers, which ensures that it is constantly evolving to meet the needs of its users.

In summary, Kubernetes is a powerful platform for managing containerized applications. Its automated deployment, scaling, and management features make it an ideal choice for organizations that want to take advantage of the benefits of containerization.

Kubernetes is an open-source container orchestration platform used to automate the deployment, scaling, and management of containerized applications. It works with different containerization tools like Docker, provides automated deployment and scaling, service discovery and load balancing, self-healing, rolling updates and rollbacks, and resource management. Its large and active community ensures it is constantly evolving to meet user needs.

**Difference between Docker and Container:**

| DOCKER | KUBERNETES |
| --- | --- |
| Single Host - Thus impacting the other containers | Cluster - Group of Nodes |
| No Auto Healing | Supports Auto Scaling |
| No Auto Scaling | ReplicaSet helps with Auto Scaling  |
| No Enterprise level support | Supports  Enterprise level support |

## Kubernetes Architecture

![tempsnip.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/30ac58d2-df83-4e3a-87c3-4765451a3e64/tempsnip.png)

[]()

Kubernetes architecture consists of a master node and worker nodes. The master node is responsible for managing the overall state of the cluster, while the worker nodes are responsible for running the containers.

The master node consists of several components, including:

- **API Server**: This component exposes the Kubernetes API, which the other components use to communicate with the cluster.
- **etcd**: This component stores the configuration data for the cluster.
- **Controller Manager**: This component is responsible for managing the various controllers that control the state of the cluster.
- **Scheduler**: This component is responsible for scheduling the containers onto the worker nodes.

The worker nodes consist of several components, including:

- **Kubelet**: This component runs on each worker node and is responsible for starting and stopping containers as directed by the API Server.
- **kube-proxy**: This component is responsible for providing network services to the containers running on the node.
- **Container Runtime**: This component is responsible for running the containers.
- **Cloud Contol Manager:** used during the integration of Cloud services like EKS,AKS etc.

## Kubernetes Pods:

A pod is the smallest and simplest unit in the Kubernetes object model. It represents a single instance of a running process in a cluster. A pod can contain one or more containers, which share the same network namespace and can communicate with each other using the localhost interface.

Pods are designed to be ephemeral and disposable. They can be created, destroyed, and recreated as needed by Kubernetes. Pods are also designed to be stateless, which means that their contents can be replaced or updated without affecting the overall state of the cluster.

Kubernetes uses pods as the basic building block for deploying and managing containerized applications. Pods can be managed directly by Kubernetes, or they can be managed by higher-level controllers like ReplicaSets or Deployments.

```yaml
apiVersion: v1
kind: **Pod**
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

1. **apiVersion**: v1 specifies the Kubernetes API version being used in this file, which is v1 for the core Kubernetes API.
2. **kind**: Pod specifies that we are creating a Kubernetes Pod object.
3. **metadata** is a section of the YAML file that contains metadata about the Pod, including its name and labels.
4. **name**: nginx-pod specifies the name of the Pod as "nginx-pod".
5. **labels** is a set of key-value pairs that can be used to organize and select objects in Kubernetes.
6. **app**: nginx is a label assigned to this Pod, which can be used to select this Pod later.
7. **spec** is a section of the YAML file that describes the desired state of the Pod.
8. **containers** is an array of container definitions for the Pod. In this case, there is only one container definition.
- **name**: nginx-container specifies the name of the container as "nginx-container".
- **image**: nginx:latest specifies the Docker image to be used for the container. In this case, it is the latest version of the nginx image.

**ports** is an array of port definitions for the container.

- **containerPort**: 80 specifies that the container should listen on port 80, which is the default port for HTTP traffic.

**Why Deployment over Pod?** POD doesn’t support Auto healing and Auto Scaling thus we go to our next services included in the code namely **Deployment** and **Replica Sets**.

## **Kubernetes Deployment:**

A **Deployment** is a higher-level object that manages the creation and scaling of ReplicaSets. It provides declarative updates for Pods and ReplicaSets. Deployments are the recommended way to manage the creation and scaling of Pods.

**Deployment** and **ReplicaSet** are two important objects in Kubernetes. Both of them are used for managing multiple instances of Pods. However, there are some key differences between them.

A **ReplicaSet** is a lower-level object that ensures a specific number of replicas of a Pod are running at any given time. ReplicaSets are useful for creating a fixed number of Pods, but they don't provide declarative updates like Deployments do.

```yaml
apiVersion: apps/v1
kind: **Deployment**
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  **replicas**: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

**Why Services?** Consider ****Pod or Deployment goes down, and it’s created again using the Auto Healing feature or Replica set but each time it is created again, the IP address of the Pod changes which affects the communication of the pod with the external world or other teams who’re working on the same service.

## Kubernetes Service:

A Kubernetes **Service** is an abstraction layer that defines a logical set of Pods and a policy by which to access them. Services enable network access to Pods, and provide load balancing and service discovery for applications running in a Kubernetes cluster.

Advantages: **Load Balancing, Service Discovery, Expose to world.**

Services can be of several types, including:

- **ClusterIP**: This is the default type of Service, which provides a stable IP address for a set of Pods within the same cluster.
- **NodePort**: This type of Service exposes the Service on a port on each node in the cluster. It provides a way to access the Service from outside the cluster.
- **LoadBalancer**: This type of Service provisions a load balancer for the Service in cloud environments. It provides a way to distribute traffic across multiple nodes in the cluster.
- **ExternalName**: This type of Service maps a Service to a DNS name, allowing applications to access the Service using a DNS name rather than an IP address.

To create a Service in Kubernetes, you need to define a YAML file that specifies the desired state of the Service. Here is an example YAML file for a simple Service:

```yaml
apiVersion: v1
kind: **Service**
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
  - name: http
    port: 80
    targetPort: 8080

```

This YAML file creates a Service named "my-service" that targets Pods with the label "app=my-app". It exposes port 80 on the Service, which is mapped to port 8080 on the Pods.

Services are a critical component of Kubernetes, as they enable communication between Pods and provide load balancing and service discovery for applications running in a Kubernetes cluster.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: **NodePort**
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```

This manifest file creates a Service object that exposes a set of Pods labeled "[app.kubernetes.io/name=MyApp](http://app.kubernetes.io/name=MyApp)" on port 80, which can be accessed from outside the Kubernetes cluster at port 30007 on each Node's IP address.

Similarly, you can change the type NodePort to LoadBalancer or the default cluster IP as per the requirement.
