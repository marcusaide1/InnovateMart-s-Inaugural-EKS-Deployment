# InnovateMart-s-Inaugural-EKS-Deployment
InnovateMart’s Inaugural EKS Deployment
Company: InnovateMart Inc.

Role: Cloud DevOps Engineer

Mission: “Project Bedrock” – Deploy our new microservices application to a production-grade Kubernetes environment on AWS.

1. Introduction & Company Background
Welcome to InnovateMart! We are a rapidly growing e-commerce startup that has recently secured Series A funding to scale our operations globally. Our engineering team has been hard at work breaking down our legacy monolithic application into a modern, scalable microservices architecture.

As our newly hired Cloud DevOps Engineer, you are entrusted with a critical, high-impact mission: to lay the foundation for our cloud infrastructure by deploying the new Retail Store Application on Amazon Elastic Kubernetes Service (EKS). This project, codenamed “Project Bedrock,” is the cornerstone of our future expansion. Your success will directly impact our ability to deliver a world-class shopping experience to our customers.

2. Your Mission
Your primary objective is to deploy the complete retail-store-sample-app to a new EKS cluster. You must automate the infrastructure setup and ensure the application is running, stable, and ready for the development team to access.

This is more than just a technical task; it’s about demonstrating your ability to think like a Cloud Engineer—prioritizing automation, security, and scalability from day one.

3. Core Requirements (Mandatory)
To successfully complete Project Bedrock, you must deliver the following:

3.1. Infrastructure as Code (IaC):

Provision all necessary AWS resources using an IaC tool of your choice (Terraform is recommended, but CloudFormation or Pulumi are also acceptable).
Your IaC code must create:
A Virtual Private Cloud (VPC) with public and private subnets.
An Amazon EKS cluster.
The necessary IAM roles and policies for the EKS cluster and node groups.
3.2. Application Deployment:

Deploy the retail-store-sample-app to your EKS cluster.
For this initial deployment, you must use the default in-cluster dependencies. This means the databases (MySQL, PostgreSQL, DynamoDB Local) and message brokers (Redis, RabbitMQ) will run as containers inside the EKS cluster.
3.3. Developer Access:

Create a new IAM user for our development team.
This user must have read-only access to the resources within the EKS cluster. The goal is to allow developers to view logs, describe pods, and check service status without being able to make changes.
Provide the necessary credentials and configuration instructions for this user.
3.4. Automation with CI/CD:

At InnovateMart, we “CI/CD everything.” You must create a CI/CD pipeline (e.g., using GitHub Actions) to automate the deployment of your Terraform infrastructure code.
Your pipeline should be built around a sound branching strategy (e.g., GitFlow). For example, pushes to feature branches could trigger a terraform plan, while a merge to the main branch triggers a terraform apply.
Ensure that AWS credentials are not hardcoded in the pipeline configuration and are managed securely.
4. Bonus Objectives (Extra Marks)
InnovateMart values engineers who go above and beyond. Completing these objectives will demonstrate your expertise in building truly production-ready systems and will be rewarded with bonus marks.

4.1. Managed Persistence Layer:

Instead of using the in-cluster databases, provision and configure managed AWS services for persistence.
AWS RDS for PostgreSQL for the orders service.
AWS RDS for MySQL for the catalog service.
Amazon DynamoDB for the carts service.
You will need to modify the Kubernetes ConfigMap and Secret manifests to point the microservices to these new AWS resources. Do not store database credentials in plain text in your Git repository.
4.2. Advanced Networking & Security:

Install and configure the AWS Load Balancer Controller in your EKS cluster.
Create a Kubernetes Ingress resource to expose the ui service to the internet via an Application Load Balancer (ALB).
Register a domain name (you can use a free service like freenom or just document the process with a placeholder) and configure it in Route 53 to point to the ALB.
Provision an SSL certificate using AWS Certificate Manager (ACM) and attach it to the ALB to enable HTTPS.
5. Deliverables
Please submit a single document containing the following:

Git Repository Link: A link to your public or private (with access provided) Git repository containing all your IaC code, CI/CD pipeline definitions, and any custom Kubernetes manifests.
Deployment & Architecture Guide: A brief document (2 pages max) that includes:
An overview of your architecture.
Instructions on how a user can access the running application.
The credentials and kubeconfig instructions for the read-only developer IAM user.
If you attempted the bonus objectives, a detailed explanation of your implementation.
6. Grading Rubric
Your work will be evaluated based on the following criteria:

Category	Task
Core: Infrastructure	EKS Cluster and VPC provisioned correctly with IaC.
IAM Roles and Policies are well-defined and follow least privilege.
CI/CD pipeline automates Terraform deployment based on a branching strategy.
Core: Application	All microservices are deployed and running successfully.
Application uses the specified in-cluster dependencies.
Core: Security	Read-only developer IAM user is created and configured correctly.
Documentation	Git repository is well-organized with a clear README.
The Deployment & Architecture guide is clear and comprehensive.
Bonus: Persistence	RDS for PostgreSQL and MySQL are correctly provisioned and integrated.
DynamoDB is correctly provisioned and integrated.
Bonus: Networking	Ingress, ALB, and Domain are correctly configured.
SSL/TLS is successfully implemented for secure access.
Good luck, Engineer. We are excited to see what you build!
