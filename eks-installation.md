### create cluster in windows
* install aws cli  in powershell
* configure aws cli
* install eksctl in powershell
* ### Elastic Kubernetes Service
* This is managed service from AWS
* EKS is easily created from a tool called as eksctl
* Install aws cli and configure authentication for aws iam user
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
 