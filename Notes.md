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
       * Equility based  `kubectl get pods -l <key>=<value>`
![preview](./Images/k4.png)
       * Inequility based ` kubectl get pods -l <key>!=<value>`
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


