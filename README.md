# End-to-End AWS Infrastructure Automation Using Jenkins and Terraform

This project demonstrates the automation of AWS infrastructure provisioning using Terraform, integrated with Jenkins for continuous integration and delivery (CI/CD). It highlights how Infrastructure as Code (IaC) and CI/CD pipelines can be combined to deliver scalable, repeatable, and automated cloud deployments.

------------------------------------------------------------
Overview
------------------------------------------------------------

The objective of this project is to deploy and manage AWS infrastructure using Terraform through a Jenkins pipeline. The Jenkins server orchestrates Terraform commands such as init, plan, and apply to automate infrastructure provisioning on AWS. The implementation showcases DevOps best practices including IaC, CI/CD, and AWS automation.

------------------------------------------------------------
Features
------------------------------------------------------------

- Automated provisioning of AWS EC2 instances and Security Groups.
- Jenkins pipeline integrated with Terraform for end-to-end automation.
- IAM-based AWS authentication through Jenkins credentials.
- Dynamic subnet and VPC detection.
- Configurable and reusable Terraform setup.
- Complete infrastructure lifecycle management (init, plan, apply, destroy).

------------------------------------------------------------
Architecture
------------------------------------------------------------

1. Jenkins server is hosted on an EC2 instance.
2. Jenkins triggers a Terraform job using a pipeline.
3. Terraform authenticates with AWS using IAM credentials.
4. Terraform provisions infrastructure including:
   - EC2 instance (m7i-flex.large)
   - 30 GB root volume
   - Security Group with SSH (22), HTTP (80), and Jenkins (8080)
5. Jenkins monitors the process and displays results.

------------------------------------------------------------
Technology Stack
------------------------------------------------------------

Category                 Tools
------------------------------------------------------------
Cloud                    AWS (EC2, IAM, VPC, Security Groups)
CI/CD                    Jenkins
Infrastructure as Code    Terraform
Operating System          Ubuntu 24.04 / Amazon Linux 2023
Scripting                 HCL, Groovy

------------------------------------------------------------
Project Structure
------------------------------------------------------------

jenkins-terraform-aws-cicd/
│
├── main.tf
├── provider.tf
├── variables.tf
├── outputs.tf
├── Jenkinsfile
├── README.md
└── screenshots/
    ├── jenkins-dashboard.png
    ├── pipeline-success.png
    ├── aws-ec2-instance.png

------------------------------------------------------------
Setup Instructions
------------------------------------------------------------

Step 1: Install Required Tools
------------------------------

sudo apt update -y
sudo apt install -y openjdk-17-jdk wget unzip
wget https://releases.hashicorp.com/terraform/1.7.0/terraform_1.7.0_linux_amd64.zip
unzip terraform_1.7.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform -v

Step 2: Install Jenkins
-----------------------

wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" \
| sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install -y jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins

Access Jenkins:
http://<EC2-Public-IP>:8080

Step 3: Configure AWS IAM
-------------------------

- Create an IAM User (terraform-jenkins)
- Attach:
  - AmazonEC2FullAccess
  - AmazonVPCFullAccess
  - IAMFullAccess
- Add credentials into Jenkins (Manage Jenkins → Credentials → AWS Credentials)

------------------------------------------------------------
Terraform Configuration Files
------------------------------------------------------------

provider.tf
-----------
provider "aws" {
  region = "ap-southeast-2"
}

variables.tf
------------
variable "region" { default = "ap-southeast-2" }
variable "instance_type" { default = "m7i-flex.large" }
variable "key_name" { default = "terraform-key" }
variable "volume_size" { default = 30 }
variable "instance_name" { default = "Terraform-Jenkins-Server" }

main.tf
-------
(data blocks, security group, EC2 instance creation)

outputs.tf
----------
output "instance_id" { value = aws_instance.jenkins_server.id }
output "public_ip" { value = aws_instance.jenkins_server.public_ip }
output "security_group_id" { value = aws_security_group.jenkins_sg.id }

------------------------------------------------------------
Jenkins Pipeline (Jenkinsfile)
------------------------------------------------------------

pipeline {
    agent any
    stages {
        stage('Initialize Terraform') { steps { sh 'terraform init' } }
        stage('Validate Terraform') { steps { sh 'terraform validate' } }
        stage('Plan Infrastructure') { steps { sh 'terraform plan' } }
        stage('Apply Infrastructure') { steps { sh 'terraform apply -auto-approve' } }
    }
}

------------------------------------------------------------
Example Output
------------------------------------------------------------

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

instance_id = "i-0419695f5906899b1"
public_ip  = "3.25.110.9"
security_group_id = "sg-0c784d6dacdb5557c"

------------------------------------------------------------
Clean Up Resources
------------------------------------------------------------

terraform destroy -auto-approve

------------------------------------------------------------
Key Learnings
------------------------------------------------------------

- Integrated Terraform with Jenkins pipelines for AWS automation.
- Automated EC2 instance provisioning and security group management.
- Managed IAM credentials securely in Jenkins.
- Demonstrated complete IaC + CI/CD workflow.

------------------------------------------------------------
Author
------------------------------------------------------------

Pakthapriyan Sivakumar
DevOps Engineer | Cloud Practitioner
