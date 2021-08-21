## Kubernetes Cluster Setup using Kubeadm and Ansible

### Deploys a 3 node Kubernetes cluster on Centos7 servers: one master and two worker nodes.

Each server has 4GB RAM and 2 cpus.

### Steps involved in setting up the Kubernetes cluster using kubeadm:
* Install Docker on all nodes.
* Install the latest version of Kubeadm, Kubelet, and kubectl on all the nodes.
* Initiate Kubeadm control plane configuration on the master node.
* Join worker nodes to the master node using the join command.
* ~~Install the Calico network plugin.~~

  Calico installation caused an error and `kube-prometheus` `prometheus-operator` pod starts in a CrashLoopBackOff state.

  `Unhandled error received. Exiting..." err="communicating with server failed: Get \"https://10.96.0.1:443/version?timeout=32s\": dial tcp 10.96.0.1:443: connect: no route to host"`

  Fix: install Calico manually.

    https://github.com/prometheus-operator/prometheus-operator/issues/2211#issuecomment-459388044

