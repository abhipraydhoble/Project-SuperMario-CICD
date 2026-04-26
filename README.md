# Project-SuperMario-CICD

---
### Create IAM role using following policies
````
AdministratorAccess
AmazonEKSClusterPolicy
AmazonEKSWorkerNodePolicy
AmazonEKS_CNI_Policy
AmazonEC2ContainerRegistryFullAccess
AmazonEC2FullAccess
AmazonVPCFullAccess
AutoScalingFullAccess
IAMFullAccess
CloudWatchFullAccess
````
### Launch Instance
  - AMI: Ubuntu
  - Instance Type: t3.medium

   ![instance](https://github.com/abhipraydhoble/Project-Super-Mario/assets/122669982/5fe51373-eaac-4f7c-9669-34c578277051)
   
  - Attach IAM to an Instance

---

### Connect to EC2-Instance
   ![connect-ec2](https://github.com/abhipraydhoble/Project-Super-Mario/assets/122669982/9d518e77-6f65-4153-acfc-790a6eaf669a)

---

### Script file to install Jenkins & Terraform (Give execute  permission and install)
````
#!/bin/bash

set -e  # exit on error

echo "🚀 Updating system..."
sudo apt update -y

# -------------------------------
# Java + Jenkins Installation
# -------------------------------

echo "☕ Installing Java (required for Jenkins)..."
sudo apt install -y fontconfig openjdk-21-jre
java -version

echo "🔐 Adding Jenkins repository key..."
sudo mkdir -p /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

echo "📦 Adding Jenkins repository..."
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

echo "🔄 Updating package list..."
sudo apt update -y

echo "⚙️ Installing Jenkins..."
sudo apt install -y jenkins

echo "▶️ Starting and enabling Jenkins..."
sudo systemctl enable jenkins
sudo systemctl start jenkins

echo "📊 Jenkins status:"
sudo systemctl status jenkins --no-pager

# -------------------------------
# Terraform Installation
# -------------------------------

echo "🔐 Adding HashiCorp GPG key..."
wget -O - https://apt.releases.hashicorp.com/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "📦 Adding Terraform repository..."
echo "deb [arch=$(dpkg --print-architecture) \
signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com \
$(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

echo "🔄 Updating package list..."
sudo apt update -y

echo "🌍 Installing Terraform..."
sudo apt install -y terraform

echo "📦 Terraform version:"
terraform -version

# -------------------------------
# kubectl Installation
# -------------------------------

echo "☸️ Installing kubectl..."

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

rm -f kubectl

echo "🔍 kubectl version:"
kubectl version --client

# -------------------------------
# AWS CLI v2 Installation
# -------------------------------

echo "🌍 Installing AWS CLI v2..."

sudo apt install -y unzip

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip -o awscliv2.zip

sudo ./aws/install --update

rm -rf aws awscliv2.zip

echo "📦 AWS CLI version:"
aws --version

# -------------------------------
# Jenkins Permissions Fix
# -------------------------------

echo "🔧 Fixing Jenkins permissions..."

sudo usermod -aG sudo jenkins || true
sudo usermod -aG docker jenkins || true

sudo systemctl restart jenkins

# -------------------------------
# Final Info
# -------------------------------

echo "✅ Setup Complete!"

echo "👉 Access Jenkins at:"
echo "http://<your-server-ip>:8080"

echo "🔑 Get initial admin password:"
echo "sudo cat /var/lib/jenkins/secrets/initialAdminPassword"

echo "🧪 Verify setup:"
echo "aws sts get-caller-identity"
echo "kubectl version --client"
echo "terraform version"
````

---

### 
