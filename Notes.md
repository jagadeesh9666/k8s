### Pods 
* Scaling: Scaling in k8s means increasing number of Pods not containers in Pod. For Scaling pods we would learn Replica set/Replication Controller etcs..

* To write manifest files [Refer Here](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/)
* How k8s identifies objects: Labels
* Label is a key pair examples are
    * app: nginx
    * version: v1.0
* Labels are used to select/query kubernetes objects
* [Refer Here](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) for official docs on labels
* Selectors in k8s help in querying objects using labels
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/5e9973ee223754f52857f4aff58e6240a690477b#diff-d1590d82e06f13a05dc9b42f83585ac90aea08e24debd9ea81f2bb7d3481d877) for the code with labels

![preview](./Images/k3.png)

* selectors are of two types
    * Equality Based Selectors (Equality- or inequality-based requirements allow filtering by label keys and values.)
       - Equility based  `kubectl get pods -l <key>=<value>`
       - Inequility based `kubectl get pods -l <key>!=<value>`

       ![preview](./Images/k4.png)
       
     
       ![preview](./Images/k5.png)
    
* Set based selectors`kubectl get pods -l '<key> in (<value>'`

![preview](./Images/k6.png)

### Interacting with containers
* In docker docker container exec -it or docker container exec    
* in k8s we have kubectl exec

![preview](./Images/k7.png)

* Interactive ` kubectl exec <pod name> -it -c <container name> -- /bin/sh `

![preview](./Images/k8.png)

 ### What happens when container goes into exited state
* Lets create a pod with ubuntu container which exits
[Refer Here](https://github.com/jagadeesh9666/k8s/commit/305fcdb1e94f142ef22afc72830dd5efd426e7ae) for the changes
* kubernetes tries to restart containers, if it fails continuous k8s keeps trying and gives the status as CrashLoopBackoff

![preview](./Images/k9.png)

* Kuberentes restarts the containers always as the restartPolicy Default value is Always, which we can change [Refer Here](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy)

 ### Container types in Pod
* init containers: These containers are created prior to actual/main containers. ideally these containers should be short lived and majorly for meeting preconditions to run your application. [Refer Here](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) for official docs
* containers: This is where we run actual applications and they are expected to be living forever (continously)
[Refer Here](https://github.com/jagadeesh9666/k8s/commit/3d7fdb7723dbcb518f983b7954afa7a728838462) for changes

![preview](./Images/k11.png)

### Pod lifecycle
[Refer Here](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/) for documents

![preview](./Images/k10.png)

 ### Resource Restrictions in Pods
[Refer Here](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) for official docs
* Limits in Resoruce Restrictions mean maximum size that will be allocated (upper bounds/limits) and request are lower limits
[Refer Here](https://github.com/jagadeesh9666/k8s/commit/1dafa628703f5a79788c29b11a7d0c46d2d374ad) for the changes

### Controllers
* Pod tries to keep containers running, but for us we need to keep Pods running according to some state, Lets understand first two categories
    * Replicas:
       - Here we have two resources ReplicationController, ReplicaSet
       - Here our desired state (spec) will be
          - number of replicas
          - pod spec
          - label selector
       - These objects try maintain the desired
    * Jobs:
       - These will run the Pods which have finite execution time period
       - Here we have two resources
           - Job
           - CronJob
### ReplicaSet
* This Resource is responsible for maintaing the desired state of number of replicase of pod .
* [Refer Here](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) for dockments

### Activity:
* Create a replica set with 3 replicas of jenkins/jenkins
* The label in selector should match with label in template , if not the pods will not get created.
![preview](./Images/k12.png)
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/00dcb36db964fdf161efc1596452f8a274ff582c?diff=split) for manifest file
![preview](./Images/k13.png)
![preview](./Images/k14.png)
* matchlabels is a equility based
* Now delete one pod and observe , ther will be new pod created to maintain desired number of replicas
![preview](./Images/k15.png) 
* Lets try set based selectors [Refer Here](https://github.com/jagadeesh9666/k8s/commit/cdbe8eb187385d7a934d2fc21fb634054abe43b8) for the changes
* Exercise: Write a Replication controller for creating 3 spc pods
[Refer Here](https://github.com/jagadeesh9666/k8s/commit/40f891f718a15b9c6fb8185464a350d187d7705f) for code changes.

### Jobs and Cron Jobs

* [Refer Here](https://kubernetes.io/docs/concepts/workloads/controllers/job/) for Offical jobs documentation and [Refer Here](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/) for Official CronJob Documentation
* job will not get completed until the created pod is deleted 
* lets create a job which creates a container for 10 seconds
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/09b797d529dcae88db78dfeac8ae30fb9518c45f) for the changes
 ![preview](./Images/k16.png)

* Lets write a Cron Job which runs spc pod with command 1d for every mins
* cornjob creates a new pod for specified time contineously.
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/d80712975b5817d4cc643b2ac14f209cf33951d2) for the changes
 ![preview](./Images/k17.png)

### Namespace
* Namespace in k8s is a logical space or logical cluster in which resources will be created
* Any resource which has a value of Namespace = true belongs in a namespace and with Namespace=false is shared across namespaces.
* [Refer Here](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) for official docs

### Service in K8s
* Every Pod when created gives a unique ip address and Name
* When Pods are scaled
* Lets create a replicaset with 3 nginx pods with label app:nginx
```
---
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
  labels:
    app: nginx
    purpose: svcdemo
spec:
  replicas: 3
  minReadySeconds: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginxc
          image: nginx:1.25
          ports:
            - containerPort: 80
              protocol: TCP
```
* We need to access nginx with in cluster, but pod ips are not reliable, so as shown in the below image, lets create a k8s service resource which gives a consistent ip and name to access all the pods matching labels (equality based selection)
![preview](./Images/k18.png)
* service in k8s is of 4 types
  * Cluster IP
  * None
  * External
  * LoadBalancer
* Lets create nginx rs and and service and view endpoints. As discussed in the class when pods get update the endpoints are reflected. Service will forward the requests to endpoints
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/6e96dfedbd8f8963237a24b503a64e15a71ebd62) for the code

### Managed Kubernetes or Kubernetes as a Service
* Managed Kuberenetes (Kubernetes as a Service) is the offerings from various cloud providers.
* In these Control-Plane is managed by Cloud Service Providers and they charge you hourly for that
* For worker nodes, storage and other resources charges are as usual.
* Popular Kuberentes offerings
* AKS (Azure Kubernetes Services)
* EKS (Elastic Kuberentes Services)
* GKE (Google Kubernetes Engine)

* K8s as a service basically means the master nodes will be managed by cloud provider

### Elastic Kubernetes Service
* This is managed service from AWS
* EKS is easily created from a tool called as eksctl
* Install aws cli and configure authentication for aws iam user
* [Refer Here](https://eksctl.io/) for yaml file for eks cluster
* Lets create a config file `eks-cluster.yaml`

```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: jaeks-cluster
  region: ap-south-1

nodeGroups:
  - name: ng-1
    instanceType: t2.medium
    desiredCapacity: 2
```
* Now create the cluster by executing `eksctl create cluster -f eks-cluster.yaml`
![preview](./Images/k19.png)

### Exposing Applications running in cluster to externally as well as internally when scaled
* Every pod gets a unique ip and name.
*Connecting from one pod to other on the basis of name/ip might not be a good idea as pods are controlled by replicasets or other controllers
* K8s has a service which helps us in connecting to pods with similar behaviour but by using labels.
* Each service gets a ip address and this is virtual ip which helps in forwarding traffic to one of the pod based on labels. This ip is called as cluster ip
* Services can be exposed to external world
![Preview](./Images/k20.png)
* Service is similar to layer 4 load balancer

### Internal Communication using k8s service (cluster ip )
* Consider the following
      * We have an alpine pod which needs to consume nginx
      * but nginx is a replica set and there can be n replicas
* Lets create a spc-rs
* Lets create a spc-svc
* Lets create a alpine pod and login into it
[Refer Here](./Images/k21.png)
* ping nginx-svc by its ip address and try accessing the web page using curl
[Refer Here](https://github.com/jagadeesh9666/k8s/tree/master/internal-cluster-ip.yml) for internal communication manifests

### External Communication using k8s service
* Some user external to k8s cluster wants to access nginx
[Refer Here](./Images/k22.png)
* Kubernetes has the service publishing types [Refer Here](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)
   * Cluster ip: internal communication
   * Node Port: k8s will expose the application on a port on every node in k8s cluster.
   [Refer Here](./Images/k23.png)
   * LoadBalancer: This is generally used with managed k8s clusters
   [Refer Here](./Images/k24.png)
   * ExternalName: Creates a CNAME record that can be used in your DNS Servers

### Health Checks/Probes for containers in k8s Pods
* [Refer Here](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) for official documents
* K8s supports 3 kinds of checks
   * liveness probe: if this check fails kuberenetes will restart the container.
   * readiness probe: if this check fails the pod will be removed from service (pod will not get requests from service)
   * startup probe: This checks for startup and until startup is ok, the other checks will be paused.
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/44bccbbef68b0e980d6d1c5c143161fa089c0940) for manifest file

### Run Pods with specific Resources (CPU/Memory)
* [Refer Here](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) for the official docs on Resource Limits
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/52391d68ece06149666b495577664d9b2913532f) for manifest file

### Container Types in Pods
* There are three types of containers
    * containers
    * initcontainers : these containers are created before actual containers create.
    * ephemeral containers : we use ephemeral containers to inspect services rather than to build applications.
### Deployment
* Deployment is a k8s object which can help in rolling out and rolling back updates
* Deployment controls replica set and replica set controls pods
![preview](./Images/k25.png)
[Refer Here](https://github.com/jagadeesh9666/k8s/commit/4ea2cb3c2d9e0323811d79c4358d8fcd6e63357d) for manifest files

### Annotations
* [Refer Here](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) for official docs
* Annotations are provided to know the change cause of a pod.

### DaemonSet
* DaemonSet is a controller which creates pod on every/selected nodes in k8s cluster
* Use cases:
   * log collectors
   * agents etc
[Refer Here](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) for official documents
* A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them
* Some typical uses of a DaemonSet are:
      * running a cluster storage daemon on every node
      * running a logs collection daemon on every node
      * running a node monitoring daemon on every node
[Refer Here](https://github.com/jagadeesh9666/k8s/commit/8f21d3d0ee44e56709dd7bab7911f0a3935edc52) for manifest files

### Node Selectors
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/0a5e971daa49098e93316afb77722c180c010fdf#diff-02e376f35caba87bf8474cbb7b5ba3edbfc0aadab038032109b8047d8d6fdea0) for manifest files
* node selector creates pod on specified node.
![Preview](./Images/k26.png)
* lets attach labels to the node
`kubectl label nodes <name of node> <key>=<value>`
![preview](./Images/k27.png)

### Headless Service
* Headless service will not have cluster ip.
* Headless service returns the ips of the pods returned by selector.
* This is used in stateful sets
* In head less service it show's only ip's of pods which are connected to that server.
```
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  selector:
    app: nginx
  type: ClusterIP
  clusterIP: None
  ports:
    - name: nginx-svc
      port: 80
      targetPort: 80
      protocol: TCP
```
### Storage Solutions in K8s
* Stateful applications store data locally. In Containers the data created locally will be lost once you delete it. So to solve this in docker we have used volumes. Volumes have a lifecycle which has no relation to container lifecycle (refer docker containers, image layers, volumes)
* IN k8s we are running docker containers, k8s is an orchestration solutions.
* Lets see what are options for storage provisioning in k8s [Refer Here](https://kubernetes.io/docs/concepts/storage/)
* The most widely used storage types
* Volumes
* Persistent Volumes
  * Storage Classes
  * Persistent Volume Claims

### Volumes
* Volumes can be mounted to containers and they have lifetime equivalent to Pods.
* Types of volumes [Refer Here](https://kubernetes.io/docs/concepts/storage/volumes/#volume-types)
* The types
    * storage on cloud
        * ebs
        * azure disk
        * efs
        * azure file
        * gcs
    * empty dir
    * hostPath
* created a volume and attached to a path in container
[Refer Here](https://github.com/jagadeesh9666/k8s/commit/c6dc5345669320d88d61c9ee0a8a7e3a6f9be0db) for manifest file 

### Persistent Volumes
* These volumes will have a lifetime different than Pod i.e. they exist even after pod is dead.
* Types of Persistance volumes
   * csi - Container Storage Interface (CSI)
   * fc - Fibre Channel (FC) storage
   * hostPath - HostPath volume (for single node testing only; WILL NOT WORK in a multi-node cluster; consider using local volume instead)
   * iscsi - iSCSI (SCSI over IP) storage
   * local - local storage devices mounted on nodes.
   * nfs - Network File System (NFS) storage

### StatefulSets
* Statefulset is like deployment with replicas. But each pod gets its own volume.
* Stateful Set is for stateful applications
* ![preview](./Images/k28.png)
* When we create replicas in Stateful Set we get predictable names
* We can access individual pod, by creating headless service and by using ...svc.cluster.local
* [Refer Here](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) for official docs

### Namespace
* Namespace is logical/virtual cluster within k8s cluster
* K8s resources will be of two categories by scope
    * Namespace resources: They belong to a namespace.
    * luster resources: They belong to a cluster
* Creating namespace: `kubctl create ns <name>`

### Config Map
* [Refer Here](https://kubernetes.io/docs/concepts/configuration/configmap/) for official docs
* Lets create a dummy config map. [Refer Here](https://github.com/jagadeesh9666/k8s/commit/473052ee1e240e06a8b6a47880f3f9626610be3a) for the changes.
* Create the config map
![preview](./Images/k29.png)
* There are four different ways that you can use a ConfigMap to configure a container inside a Pod:
   * Inside a container command and args
   * Environment variables for a container
   * Add a file in read-only volume, for the application to read
   * Write code to run inside the Pod that uses the Kubernetes API to read a ConfigMap
* Load config map data as Environment variables. [Refer Here](https://github.com/jagadeesh9666/k8s/commit/6dfdad373fff78fb8e07277085524e226f182ee8) for pod spec which loads the config map as environmental variables
![preview](./Images/k30.png)

### Secrets
* A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key. Such information might otherwise be put in a Pod specification or in a container image. Using a Secret means that you don't need to include confidential data in your application code.
* Secrets can be created from kubectl [Refer Here](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-kubectl/#use-raw-data) and manifest files [Refer Here](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-config-file/#edit-secret)
* secret's are same as configmaps , the difference is here we use encripted data .
* encript the data with base 64 algorithem.
* by default kubernetes support base 64.

### Helm
* Helm is a package manager for k8s.
* In helm we have repositories where charts are defined
* An helm chart is analogus to package (apt package)
* install helm [Refer Here](https://helm.sh/docs/intro/install/)
* Manifests are static in nature, to add reusability and dynamic nature to manifests we have two options
     * helm:
         * this uses templated approach
         * this was present from earlier days of k8s
     * kustomize:
         * this uses override approach
         * this is natively supported in kubectl (recent additions)

### Kustomize
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/1804ab45181d76ea36b67d265318aad2384683cb) for manifests
* [Refer Here](https://kustomize.io/) Kustomize
* [Refer Here](https://www.vultr.com/docs/how-to-configure-kubernetes-resources-with-kustomize/) for tutorial from vultr to use kustomize
* [Refer Here](https://github.com/jagadeesh9666/k8s/commit/c9846029965716003781d70921d34750060f47ab) for manifest files