## Provision an AWS EKS Cluster with 3 nodes

* `vpc.tf` provisions a VPC, subnets and availability zones using the AWS VPC Module.
* `security-groups.tf` provisions the security groups used by the EKS cluster.
* `eks-cluster.tf` provisions the autoscaling workers group using the AWS EKS Module.
* `outputs.tf` defines the output configuration
* `versions.tf` sets providers versions.
*  `kubernetes.tf` Kubernetes provider.
  
Before destroying, run `terraform plan -refresh-only`, otherwise terraform might throw errors.





## Connect to the AWS EKS cluster

After a successful deployment terraform creates a files named `kubeconfig_my cluster name` in the project's folder 

1. Check the current identity to verify that you're using the correct credentials that have permissions for the Amazon EKS cluster:


`aws sts get-caller-identity`


2. Create or update the kubeconfig file for your cluster:


`aws eks --region region update-kubeconfig --name cluster-name`
```
output:
Added new context arn:aws:eks:us-east-2:648826012845:cluster/education-eks-yPTT1J1m to /home/george/.kube/config

# By default, the configuration file is created at the kubeconfig path ($HOME/.kube/config) 
# in your home directory or merged with an existing kubeconfig at that location.
```

`export KUBECONFIG=/path/to/kube/config`



From Stackoverflow:
I just alias the kubectl command into separate ones for my dev and production environments via .bashrc

`alias k8='kubectl'`

`alias k8prd='kubectl --kubeconfig ~/.kube/config_prd.conf'`

I prefer this method as it requires me to define the environment for each command
whereas using an environment variable could potentially lead you to running a command within the wrong environment


3. Test your configuration:


`kubectl get pods --all-namespaces --kubeconfig ~/.kube/config`
```
# output:
[george@fedora-34 learn-terraform-provision-eks-cluster]$ kubectl get pods --all-namespaces --kubeconfig ~/.kube/config 
NAMESPACE     NAME                       READY   STATUS    RESTARTS   AGE
kube-system   aws-node-64pl4             1/1     Running   0          5m10s
kube-system   aws-node-8kkfz             1/1     Running   0          5m28s
kube-system   aws-node-lkvdj             1/1     Running   0          5m19s
kube-system   coredns-5c778788f4-d44lv   1/1     Running   0          9m39s
kube-system   coredns-5c778788f4-kprlx   1/1     Running   0          9m39s
kube-system   kube-proxy-dt67d           1/1     Running   0          5m28s
kube-system   kube-proxy-m9zk6           1/1     Running   0          5m10s
kube-system   kube-proxy-zzfh4           1/1     Running   0          5m19s
```


`kubectl get svc`
```
# output:
[george@fedora-34 learn-terraform-provision-eks-cluster]$ kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   10m
```

4. Deploy apps.

```yaml
# Deploy "hello world app" locally:

# deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-kubernetes
spec:
  selector:
    matchLabels:
      name: hello-kubernetes
  template:
    metadata:
      labels:
        name: hello-kubernetes
    spec:
      containers:
        - name: app
          image: paulbouwer/hello-kubernetes:1.8
          ports:
            - containerPort: 8080
```

`kubectl apply -f deployment.yaml`

`kubectl port-forward hello-kubernetes-6db5bf56c6-zbx6p 8080:8080`

Open browser and go to http://localhost:8080/.



If you want to route live traffic to the pod, you should have a more permanent solution.

Use `type: LoadBalancer` to expose your Pods.

```yaml

# service-loadbalancer.yaml

apiVersion: v1
kind: Service
metadata:
  name: hello-kubernetes
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 8080
  selector:
    name: hello-kubernetes
```

`kubectl apply -f service-loadbalancer.yaml`

`kubectl describe service hello-kubernetes`

Open browser and go to LoadBalancer ingress url.

