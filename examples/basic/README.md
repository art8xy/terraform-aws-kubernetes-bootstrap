# Basic Bootstrap Example
This example demonstrates how to use the `terraform-aws-kubernetes-bootstrap` module to install Helm charts into an EKS cluster.

### Login
Login to your AWS account using the cloud CLI before running the Terraform commands.
```bash
aws login
```

### Apply
Run the following commands to initialize the Terraform, create an execution plan, and apply the infrastructure.
```bash
terraform init
terraform plan
terraform apply
```

After the EKS cluster is created, the module invokes the Lambda function to connect to the Kubernetes API and install Helm charts.

## Verification
```bash
aws eks --region us-east-1 update-kubeconfig --name example-cluster
kubectl get pods --namespace monitoring
```

You should see the `grafana` pod running in the cluster.

## Cleanup
```bash
terraform destroy
```

Make sure to cleanup the Lambda function and any other resources created by the module to avoid incurring unnecessary costs.
