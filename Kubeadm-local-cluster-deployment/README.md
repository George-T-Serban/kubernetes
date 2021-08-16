## Kubernetes Cluster Setup using Kubeadm and Ansible

### Deploys a 3 node Kubernetes cluster on Centos7 servers: one master and two worker nodes.

Each server has 4GB RAM and 2 cpus.

### Steps involved in setting up the Kubernetes cluster using kubeadm.
* Install Docker on all nodes.
* Install Kubeadm, Kubelet, and kubectl on all the nodes.
* Initiate Kubeadm control plane configuration on the master node.
* Join worker nodes to the master node using the join command.
* Install the Calico network plugin.

Kubernetes metrics server not installed.