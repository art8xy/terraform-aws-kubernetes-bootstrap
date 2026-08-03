# AWS Kubernetes Bootstrap
The `terraform-aws-kubernetes-bootstrap` module simplifies private bootstrapping of EKS clusters.  
The module uses a serverless AWS Lambda function to configure the Kubernetes cluster and install Helm charts.  
The Lambda is deployed inside the EKS VPC, allowing it to access the Kubernetes API without exposing it publicly. 

### Features
* Deploys a Lambda function inside the EKS VPC
* Supports private access to the Kubernetes API
* Authenticates to EKS using AWS IAM
* Installs and manages Helm charts
* Keeps Kubernetes bootstrap operations within AWS

### How it works
Terraform creates the Lambda function, required IAM and VPC access configuration.  
After deployment, Terraform invokes the function with the configured Helm charts.

![Architecture](https://raw.githubusercontent.com/art8xy/terraform-aws-kubernetes-bootstrap/main/assets/images/architecture.png)

The Lambda function:
1. Retrieves the EKS cluster configuration.
2. Generates an EKS authentication token.
3. Configures a temporary kubeconfig.
4. Connects to the Kubernetes API.
5. Installs the configured Helm charts.

This makes it possible to provision and bootstrap private EKS clusters entirely through Terraform, without exposing the Kubernetes API publicly or requiring the Terraform execution environment to have network access to the cluster.

### Prerequisites
The following tools must be installed in the environment running Terraform.  
They are used to build and package the Lambda deployment artifact:
- `zip`
- `tar`
- `curl`
- `pip3`
- `python3`

### Example
Below is a basic example of how to use the module to install the `prometheus` Helm chart.
```hcl
module "bootstrap" {
  source = "art8xy/kubernetes-bootstrap/aws"
  version = "1.0.0"

  cluster = aws_eks_cluster.this
  charts = {
    prometheus = {
      chart      = "prometheus"
      repository = "https://prometheus-community.github.io/helm-charts"
      namespace  = "monitoring"
      values = {
        server                   = { name = "server" }
        alertmanager             = { enabled = false }
        kube-state-metrics       = { enabled = false }
        prometheus-node-exporter = { enabled = false }
        prometheus-pushgateway   = { enabled = false }
      }
    }
  }
}
```
