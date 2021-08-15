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


From Stackoverflow:
I just alias the kubectl command into separate ones for my dev and production environments via .bashrc

`alias k8='kubectl'`

`alias k8prd='kubectl --kubeconfig ~/.kube/config_prd.conf'`

I prefer this method as it requires me to define the environment for each command
whereas using an environment variable could potentially lead you to running a command within the wrong environment


3. Test your configuration:

`kubectl get pods --all-namespaces --kubeconfig ~/.kube/config`

`kubectl get svc`

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

