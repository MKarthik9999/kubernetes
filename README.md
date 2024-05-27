# Kubernetes
1. kubectl get nodes -->  Lists all nodes in the cluster.
2. kubectl cluster-info --> Displays the endpoint of the Kubernetes master and services.
# What is a Pod?
* A Pod is the smallest in Kubernetes, in Kubernetes we can’t directly deploy a container instead we create a pod and deploy a container in that pod.
* A pod can have one or more containers, one container per pod is the ideal use case, the main container might be running our main application, and other containers are used to support our main application container. _**The other containers are called sidecar containers or helper containers**_.
* In docker we create containers, and those containers are managed individually but when we create a pod in Kubernetes the containers created in those pods can be managed all at once and these containers are deployed in a shared environment so they can communicate with each other.
* Pods are typically created and managed using a higher-level abstraction such as _**Deployments, Replicasets, or statefulsets which provide additional features like scaling, Roling updates, and self-healing capabilities**_.
# Important command related to Normal Pods
> 1. kubectl get pods -> will list all the pods
> 1. kubectl get pods -o wide -> will list all the pods but with more information.
> 1. kubectl describe pods -> will describe more information about pods.
> 1. Kubectl apply -f <file-name.yml> -> To create a pod from a file
> 1. kubectl delete pods < pod-name > -> deletes a pod
> 1. kubectl delete pods --all -> deletes all pods in the namespace
> 1. kubectl port-forward < pod-name > < local-port >:< container-port > -> helps to expose our application to outside world

# What is a Deployment?
* Deployment is an _**abstraction**_ that allows you to _**describe the desired state of your application**_.
* When you create a deployment, you specify the desired state of your application by defining the number of replicas and other configuration parameters. Kubernetes then _**ensures that the actual state matches the desired state**_.
* If there is **any difference in them** then Kubernetes takes care of it by _**either adding or removing the pods as necessary**_.

# Important Commands related to Depoyment Pods:
> 1. _**kubectl apply -f web-app.yaml**_ --> Create/Apply/Update Deployment
> 2. _**kubectl get deployments**_ --> Check Deployment and Pods (kubectl get pods)
> 3. _**kubectl scale deployment web-app**_ --replicas=5 --> Scale Deployment
> 4. _**kubectl rollout status deployment web-app**_ --> Rollout (updating to newer versions) status
> 5. _**kubectl rollout undo deployment web-app**_ --> Rollback to previous version
> 6. _**kubectl rollout status deployment web-app**_ --> Rollback status
> 7. _**kubectl delete deployment web-app**_ --> Delete Deploymen


# What is a Replicaset?
* ReplicaSet _**ensures that a specified number of replica pods are running at any point in time**_.
* It is ***responsible for maintaining the desired replica count and managing the lifecycle of the pods***.
* They help in achieving _**high availability and scalability**_ by automatically scaling the number of replicas up or down based on the specification.
* When you create a replica set, ***you specify the desired number of replicas and provide a template for creating the pods***.

**_If we deploy pod as a normal pod then it will running as a single pod there will be no HIGH AVAILABILITY, but if we create a pod with multiple copies using a DEPLOYMENT, it provides HIGH AVAILABILITY, the deployment internally creates a REPLICA SET and the replica set ensures that the actual state matches the desired state._**

# Any manifest file (either pod or deployment or replicaset) **_there are four primary attributes:_**
1. apiVersion:
2. kind: which has the information about the kind of the maifest file (either pod or deployment or replicaset)
3. metadata:
4. spec:

# Kubernetes Sevices:
# What is a Kubernetes Service and why do we use it
* Normally, in terms of networking if any device wants to communicate with any other devices within the same network or outside that network it has to happen through the IP address only.
* similarly in Kubernetes if any pod needs to communicate with other pods or to communicate outside of the Kubernetes cluster it has to happen through the IP addresses only.
* whenever we create a pod or create multiple pods within a deployment, Kubernetes assigns an individual Ip address to those pods, since we know that the lifecycle of these pods is ephemeral (meaning pods don't last for a long time) their IP addresses will change every time whenever the pod is deleted and recreated, the connections that are communicating with those kinds of pods will be terminated because of the change in the IP address.
* Even though a new pod is created for communication there will be no communication because the Ip address will be different, the Ip address of the newly created pod has to be updated to whoever it was having communication before, to have the communication.
* It is very difficult and complex process to update new Ip address every time whenever a pod is deleted and recreated.
* To overcome this problem we use **_Kubernetes services_**
* so whenever we are creating a deployment _**we use selectors**_ to deploy pods in a group with a same group name, so whenever a pod is deleted and recreated it might have different Ip address but the newly created pod will still belongs to same deployment group.
* Using that Group name we can be able to commicate within the kubernetes cluster and outside the cluster.
* _**we use that group name while we create a kubernetes service**_ so that the service can be able to detect whenver a pod is deleted and recreated it can be able to detect those pods with the help of the group name(selectors) rather than using the IP addresses.
* so, whenever a new request is arrived to kubenrnetes service it forwards that request to the pods (individual) or pods (releated to Deployment) based on the selectors mentioned while created, even though if any pods are deleted and created they will be updated because the recreated pods uses the same selector.
* kubectl get services -o wide
* kubectl describe services *service-name*



* ![image](https://github.com/MKarthik9999/Kubernetes/assets/88875317/afc0a91e-1e95-437c-9691-7b1c00301878)

# Types of Services in Kubernetes services:
# 1. ClusterIp:
   * It is used for internal communication within the cluster.
   * This is the default service type used by the Kubernetes service.
   * This is the most secure service type in the kubernetes services.
   * If we create a clusterIp service and if we attach that service to any of our deployments, then the pods deployed in that deployment can be able to communicate with each other.
   * we can check using the following commands.
      * Find pod names using the following command:
          * _**kubectl get pods**_
      * Enter into one of the pods from the list of the pods using the following command:
          * _**kubectl exec -it <source-pod-name> -- /bin/sh**_
      * Inside that pod we can test the communication ****using the service name****:
          * _**curl internal-service:80**_
      * If we can be able to see our website deployed in the pods, then we can say that there is communication between the pods.
      * we can also see it using the ip-address of the other pods and try to ping one pod from another pod
          * _**curl ip-address-of-the-destination-pod**_
# 2. Node Port:
   * A NodePort service exposes a set of pods to the outside world on a static port of each node in the cluster.
   * This means that the service becomes accessible externally through any of the Kubernetes nodes, allowing traffic to reach the pods.
   * This internally uses the ClusterIp for internal communication.
   * This is mainly used for testing and development. This is not recommended for production use cases.
     ![image](https://github.com/MKarthik9999/Kubernetes/assets/88875317/81544f00-0bd8-4613-ae5f-a103d98ae379)
# 3. Load Balancer:
   * A LoadBalancer service exposes your application to the external world in a scalable and reliable (secure) manner.
   * NodePort service also exposes our application to the external world, but it exposes the application on a specific node, which causes security issues because we are exposing our application on the node itself.
   * If we try to create a load balancer service, the cloud provider automatically creates a load balancer and provides an IP address for the load balancer for our application.
   * The created load balancer distributes incoming traffic among multiple instances of your application running in the cluster.
   * This also uses the internal ClusterIP for internal communication.
  ![image](https://github.com/MKarthik9999/Kubernetes/assets/88875317/8717e13a-c39c-474e-a906-b42496708462)
   * Using load Balancer service is the **secured way of getting traffic to our pods/website** but it is a very costly approach because we have to attach a new load balancer every time we create a new service, to overcome this problem using **Kubernetes Ingress**

To sum up, there are three types of services commonly used and they are ClusterIp, NodePort and LoadBalancer
* LoadBalancer is very helpful in exposing our application to the external world in a simple, secure, and reliable manner
* But if we just want to test to check our application we can expose our application to the external world using a static port on any of our worker nodes using the NodePort service (only for testing purposes)
* If we don't want to expose our application at all to the outside world we can use the clusterIp service.
# Common Interview Questions on Services:
1. _**What is a Service in Kubernetes?**_
* A service is an abstraction that provides a consistent way to access and communicate with a set of pods.
* It acts as a stable network endpoint for accessing the pods enabling inter-pod communication and load balancing.
2. _**What is a ClusterIp service?**_
* This is the default service type, This service type is only used for internal communication. 
3. _**What is a NodePort Service?**_
* This service exposes the application on each node on a specific port, This service is accessible to the external world using any of the worker node's IP address.
* Only used for testing and development purposes.
4. _**What is a LoadBalancer Service?**_
* uses cloud providers' load balancers to distribute the external traffic to the nodes. This is the most secure type of service to use for the Kubernetes clusters.
5. _**How the POD Communication happens in Kubernetes?**_
* In Kubernetes we have a DNS service called CoreDNS, when a pod is created from a deployment or replica set, the pods are assigned a DNS name based on its metadata, This DNS name is used by 
  the other pods to discover and communicate with the pod. This is handled by CoreDNS which maps the DNS name to the IP address of the pod. 
6. _**Which service is secure in Kubernetes?**_
* clusterIp service is the most secure service type in Kubernetes because it creates internal-only service within the cluster making sure the services within the cluster are not accessible 
  to the external world making it only accessible within the cluster.
7. _**How is a service mapped to its respective pods?**_
* Using labels and selectors
* labels are key-value pairs that are attached to Kubernetes objects, including pods and services.
* selectors are used to match labels on objects.
8. _**How do the containers in a pod communicate with each other?**_
* using localhost
9. _**Explain 4 commands to work with the services.**_
* kubectl get services
* kubectl describe service *service-name*
* kubectl edit service *service-name*
* kubectl delete service *service-name*
10. _**NodePort allocation starts from?**_
* From port 30,000 it doesn't use any port less than this.
11. _**How to look at which pods are mapped to a service?**_
* kubectl describe service *service-name*

# Kubernetes Ingress
In the concept of cloud computing, we have two kinds of load balancers and they are:
1. Network Load Balancer (Layer-4)
2. Application Load Balancer (Layer-7)
* Network Load Balancers which are resided at Layer-4 are used for just forwarding the network traffic to the backend device without any modifications like no decision making.
* Application Load Balancers that reside at Layer-7 are used not only for forwarding the network traffic but also for routing traffic based on content and performing advanced request routing such as hosting different kinds of domains under the single Load Balancer, for example, www.facebook.com, www.google.com, www.amazon.com are three websites that we want to host in that case we don't need to have three load balancer for these, instead we can just have one load balancer when the client hits facebook.com the load balancer forwards the traffic to wwww.facebook.com

* In the Kubernetes services we have a LoadBalancer service, which is  a Layer-4 load balancer that is just used to forward the network traffic to its backend devices. If we want to have a Layer-7 Load Balancer we must deploy an _**Ingress Controller**_ which is a workload component.
![image](https://github.com/MKarthik9999/Kubernetes/assets/88875317/665c7a2d-807f-4dd9-9c48-4f38388aa5fb)
* For example, from the above picture if the user wants to hit app.example.com, the request first will be reached by the Kubernetes Ingress controller, the controller then checks for the resources that match the app.example.com and routes the traffic to that service (website). Resources are like having records for a specific service.
# Ingress Controller
* The ingress controller is the brain of the Ingress that manages and operates the ingress resources.
* Each cloud Provider provides their proprietary ingress controller.
* The most used thirty-party ingress controller is the Nginx ingress controller.
# Ingress Resource
* The Ingress Resource is your way of telling the Ingress Controller how to manage incoming traffic and where to send it inside your Kubernetes cluster
* It acts as a Layer-7 Load Balancer and allows for more advanced traffic routing compared to normal NodePort and LoadBalancer services
* Using Ingress services, we can define complex routing rules and multiple backend services.
* The Ingress controller will handle the implementation of these rules allowing external traffic to be efficiently and securely routed to the appropriate services within the cluster.

In simple words, the Ingress controller is the traffic cop and the Ingress Resources are the traffic rules, just like how the traffic cop makes sure that everyone follows traffic rules in the same way the Ingress controller makes sure that the services work based on the Ingress resources.

# Kubernetes ConfigMaps and Secrets
# ConfigMaps:
* A ConfigMap in Kubernetes is like a container for storing configuration data that your applications might need.
* It holds key-value pairs, where each key represents a specific configuration item, and the value is the actual setting or data.
* It holds information such as settings, environment variables, or any kind of configuration your application uses.
* This allows you to keep configuration separate from your application code, making it easier to manage and update settings without changing the actual application.
* Think of it as a way to keep your application flexible and adjustable by storing its configurable aspects outside the main code.
* **Example use-case:** You have a microservices application running in Kubernetes, and each microservice needs to connect to a common MySQL database. Instead of hardcoding the database connection details like database name, database port, etc, in your application code, you use ConfigMaps to store configuration settings and if we need to modify the settings we can just modify them in the configmaps rather than changing them in our main code.
# Secrets:
* A Secret is like a secure vault for storing sensitive information such as passwords, API keys, or certificates.
* It provides a way to keep this confidential data separate from your application code.
* Secretes also serve the same purpose as ConfigMaps but in a more secure way by protecting sensible data from hackers.
* **Example use-case:** You have a web application running in Kubernetes, and it needs to connect to a MySQL database. Instead of hardcoding the database credentials like the user name and password in your application code, you use a Kubernetes Secret to store this sensitive information securely.

