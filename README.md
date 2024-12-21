# DevOps_Challenge

### Task 1 Infrastructure Provisioning

##### Prerequisites

1. AWS Account - With all necessary permissions for IAM User.
2. AWS CLI - Command Line Interface to interact with AWS Services.
3. aws configure - (Enter the AWS credentials - Access key, secret access key, set a region)
4. kubectl - for managing kubernetes cluster
5. Cloud Formation Template - A script file for provisioning the infrastructure (eks_s3.yaml)
6. The placeholders in the template like Subnet IDs, Security Group IDs, and Bucket Name should correclty replaced with actual values (I used default VPC subnets from 2 different availability zones and default security group id).

##### Instructions to Execute

1. Validate the CloudFormation Template (eks_s3.yaml)

   ```bash
   aws cloudformation validate-template --template-body file://eks_s3.yaml
   ```

2. Deploy the CloudFormation Stack

   ```bash
       aws cloudformation create-stack \
   --stack-name EKSS3Stack \
   --template-body file://eks_s3.yaml \
   --capabilities CAPABILITY_NAMED_IAM
   ```

3. If Updating the Existing Stack

   ```bash
   aws cloudformation update-stack \
   --stack-name EKSS3Stack \
   --template-body file://eks_s3.yaml \
   --capabilities CAPABILITY_NAMED_IAM
   ```

4. To Monitor the Deployment

   ```bash
   aws cloudformation describe-stacks --stack-name EKSS3Stack
   ```

5. Update kubeconfig to Connect to the EKS Cluster

   ```bash
   aws eks update-kubeconfig --region <region> --name MyEKSCluster
   ```

6. Verification of Worker Nodes

   ```bash
   kubectl get nodes
   ```

7. Cleanup Instructions (Optional)

- Delete the CloudFormation stack:
  ```bash
      aws cloudformation delete-stack --stack-name EKSS3Stack
  ```
- Verify that all resources (EKS cluster, S3 bucket, worker nodes) are deleted from the AWS Management Console.

### Task 2 Container Deployment

##### Prerequisites

1. Amazon EKS Cluster - An active EKS cluster with worker nodes set up.
2. kubectl - Configured to connect to EKS cluster.
   ```bash
   aws eks update-kubeconfig --region <region> --name MyEKSCluster
   ```
3. Helm - Installed and configured.

4. Create helm chart
   ```bash
   helm create ngnix-eks-chart
   ```
5. The values.yaml file is configured based on the task requirement (Specifies the NGINX Docker image, Exposes the service as a LoadBalancer)

##### Instructions for deploying Helm Chart

1. Run the deployment command by navigating to the chart directory

   ```bash
   helm install ngnix-release ./ngnix-eks-chart
   ```

2. Verify the deployment using follwoing commands

   ```bash
   kubectl get pods
   ```

3. Expose the Service - checking whether it is exposed via a loadbalancer, if it is an EXTERNAL_IP will be displayed.

   ```bash
       kubectl get svc
   ```

4. Access ngnix service by opening the http://EXTERNAL_IP in the browser.

5. Cleanup Instructions (Optional)

- Uninstall the Helm Chart

  ```bash
    helm uninstall ngnix-release
  ```

- Verify cleanup by executing the commands

  ```bash
      kubectl get pods
      kubectl get svc
  ```

### Task 3 CI/CD Pipeline to Build and Push Docker Image to Amazon ECR

##### Prerequisites

1. AWS Account - With an ECR repository created.
2. GitHub Repository - Should include the Dockerfile in the root directory and a workflow file in .github/workflows/ngnix_ecr_pipeline.yml.
3. AWS IAM User - with ECR permissions
4. GitHub Secrets - Add AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY

##### Instructions for deploying CI/CD Pipeline

1. Commit and push the Dockerfile and workflow file to the main branch of github repository.

```bash
git add Dockerfile .github/workflows/ngnix_ecr_pipeline.yml
git commit -m "Add Dockerfile and GitHub Actions workflow"
git push origin main
```

2. In ACTION of GITHUB REPOSITORY select the running workflow (Build and Push to Amazon ECR).

3. Verify the Docker Image in Amazon ECR by logging into Amazon ECR Console.

4. Cleanup (Optional)

- Delete the ECR Repository.
- Remove Local Docker Resources.
