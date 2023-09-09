* to install kuberneties we required kubeadm, kubectl, kubelet to be present in all master and worker nodes.
* Run the below script to install docker on ubuntu user

```
curl -fsSL https://get.docker.com -o install-docker.sh
sh install-docker.sh
sudo usermod -aG docker ubuntu
```
* [Refer here] (https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)