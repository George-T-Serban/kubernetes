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

* Check the current identity to verify that you're using the correct credentials that have permissions for the Amazon EKS cluster:


`aws sts get-caller-identity`

* Create or update the kubeconfig file for your cluster:

`aws eks --region region update-kubeconfig --name cluster-name`


