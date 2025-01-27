![1](https://github.com/user-attachments/assets/402e161f-c40f-4f48-99cb-20f082bbae94)

# WordPress and MySQL Docker Setup

This project contains the setup to run WordPress with MySQL using Docker. Below are the detailed steps to build, tag, push, and run the containers for both WordPress and MySQL.

## 1 Prerequisites

If you are new to this repository then fork this repository and also like it so that I know you liked my work.

## 2 Prerequisites
Before starting, make sure you have Docker, Terraform, AWS CLI & Kind installed on your machine. 

## 3 Configure Terraform & AWS CLI : 
Reference Install Terraform Official Website 
```bash
https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
```
Reference Install Terraform | AWS CLI Youtube
```bash
https://www.youtube.com/watch?v=sNxDvxRk56s
```

## 4 Create AWS Group & Policy : 
Reference Create AWS IAM User Access Key & Secret Key 
```bash
https://youtu.be/03x9PPkXLN0?si=ojN3YT9HXmynBDgW
```

## 5 Create IAM user & Accees Key & Secret Key  : 
Reference Create AWS IAM User Access Key & Secret Key 
```bash
https://youtu.be/SoBlB2K2ULk?si=oNJaPToOsmg2HFvp
```

## 6 Create EC2 Instance using Terraform  : 
```bash
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"  # Example AMI ID (choose correct one for your region)
  instance_type = "t2.micro"  # Use "t2.micro" for free-tier eligible instance
  
  tags = {
    Name = "TerraformExampleInstance"
  }
}
```
## 7 Docker Install Commands
```bash
sudo apt-get update -y 
sudo apt-get upgrade -y
sudo apt-get install docker.io
sudo apt-get install docker-compose-v2 -y
```
Add User in Docker Group
```bash
sudo usermod -aG docker $USER
newgrp docker
sudo systemctl start docker
sudo systemctl status docker
sudo systemctl enable docker
```
## 8 Installing KIND and kubectl
Create a shell script file named kind_cluster.sh and paste the following commands into it 
```bash
#!/bin/bash

[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64

chmod +x ./kind
sudo cp ./kind /usr/local/bin/kind

VERSION="v1.30.0"
URL="https://dl.k8s.io/release/${VERSION}/bin/linux/amd64/kubectl"
INSTALL_DIR="/usr/local/bin"

curl -LO "$URL"
chmod +x kubectl
sudo mv kubectl $INSTALL_DIR/
kubectl version --client

rm -f kubectl
rm -rf kind

echo "Available on Github Burhan1009 kind & kubectl installation complete."
```
Give the file Permission 
```bash
 sudo chmod 777 kind_cluster.sh
```
then install 
```bash
 ./kind_cluster.sh
```
## 9 Check Version's 

```bash
docker --version
kind --version
aws --version
terraform --version
```
## Docker Installing Steps 
### 10. Navigate to the `wordpress` directory and build the WordPress Docker image
```bash
cd wordpress/
docker build -t burhan503-wordpress .
```
### 11. Navigate to the MySQL directory and build the MySQL Docker image
```bash
cd ../mysql/
docker build -t burhan503-mysql .
```
### 12 . View the Docker images
```bash
docker ps
docker images
```
### If you have previously deployed any application through Docker, the Docker images remain stored. To remove them, use this command.
```bash
docker image prune -a -f
docker stop container id
docker rm container id 
```
### 13 This guide walks you through building, tagging, pushing, and running WordPress and MySQL containers using Docker.
```bash
cd wordpress/
docker build -t burhan503-wordpress .
```
```bash
cd ../mysql/
docker build -t burhan503-mysql .
```

### 14. Tag Docker images for versioning 
After building the images, tag them for versioning:
```bash
docker login -u burhan503
docker tag burhan503-wordpress:latest burhan503/burhan503-wordpress:v1.0
docker tag burhan503-mysql:latest burhan503/burhan503-mysql:v1.0
```
### 15. Create a custom Docker network
To allow WordPress and MySQL containers to communicate, create a custom Docker network:
```bash
docker network create wordpress-network
```

### 16. Run the MySQL container
Run the MySQL container with the necessary environment variables:
```bash
docker run -d --name mysql_db --network wordpress-network \
  -e MYSQL_ROOT_PASSWORD=wordpress_user_password \
  -e MYSQL_DATABASE=name_your_wordpress_database \
  -e MYSQL_USER=wordpress_user \
  -e MYSQL_PASSWORD=wordpress_user_password \
  burhan503-mysql:latest
```

### 17. Run the WordPress container 
Run the WordPress container and link it to the MySQL container:
```bash
docker run -d --name wordpress --network wordpress-network -p 8000:80 \
  -e WORDPRESS_DB_HOST=mysql_db:3306 \
  -e WORDPRESS_DB_USER=wordpress_user \
  -e WORDPRESS_DB_PASSWORD=wordpress_user_password \
  -e WORDPRESS_DB_NAME=name_your_wordpress_database \
  burhan503-wordpress:latest
```

### 18. View running containers
Check that both the WordPress and MySQL containers are running:
```bash
docker ps
```

### 19. Access the WordPress application
Your WordPress application should now be accessible at:
```
http://localhost:8000
```

### 20. Stop and remove containers
To stop the running containers:
```bash
docker stop wordpress mysql_db
```
To remove the stopped containers:
```bash
docker rm wordpress mysql_db
```
# Setting Up KIND and Kubernetes Cluster
## 21. Installing KIND and kubectl**

Create a shell script file named `kind_cluster.sh` and paste the following commands into it:

```bash
#!/bin/bash

[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64

chmod +x ./kind
sudo cp ./kind /usr/local/bin/kind

VERSION="v1.30.0"
URL="https://dl.k8s.io/release/${VERSION}/bin/linux/amd64/kubectl"
INSTALL_DIR="/usr/local/bin"

curl -LO "$URL"
chmod +x kubectl
sudo mv kubectl $INSTALL_DIR/
kubectl version --client

rm -f kubectl
rm -rf kind

echo "Available on Github Burhan1009 kind & kubectl installation complete."
```
## 22. Setting Up the KIND Cluster
Directory : kind-cluster
Create a configuration file named `kind-cluster-config.yaml` with the following content:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

nodes:
- role: control-plane
  image: kindest/node:v1.31.2
- role: worker
  image: kindest/node:v1.31.2
- role: worker
  image: kindest/node:v1.31.2

```
-  cd kind-cluster/
-  create cluster 
```yaml
 kind create cluster --name=wordpress-namespace --config=config.yml
```
- Get cluster
```bash
kind get clusters
```
-- Delete Cluster
```bash
kind delete cluster --name <CLUSTER_NAME>
```
##  23 Verify the cluster:
```bash
kubectl get nodes
kubectl cluster-info
```
## 24 Ensure that you have the following tools installed:
```yaml
- [Docker](https://www.docker.com/get-started)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/)
```
## 25 To verify that the cluster has been created successfully, run:
```bash
kind get clusters
```
## 26 Apply Kubernetes Configurations This is Kubernetes file structure 
```yaml
k8s-manifests/
├── wordpress/
│   ├── wordpress-deployment.yaml
│   ├── wordpress-service.yaml
│   ├── wordpress-hpa.yaml
│   ├── wordpress-pv.yaml
│   ├── wordpress-pvc.yaml
│   ├── wordpress-configmap.yaml
│   ├── wordpress-ingress.yaml
├── mysql/
│   ├── mysql-deployment.yaml
│   ├── mysql-service.yaml
│   ├── mysql-pv.yaml
│   ├── mysql-pvc.yaml
│   ├── mysql-configmap.yaml
│   ├── mysql-secret.yaml
```
## 27 Next, apply the Kubernetes manifests to set up your WordPress and MySQL resources Recommended Sequence for Deployment.
#### 1 Apply Namespace.yml file - cd k8s-manifests/Mysql
```yaml
kubectl apply -f namespace.yml 
```
#### Verify Namespace 
```yaml
kubectl get namespaces
```
#### 2 MySQL Resources Create Persistent Volume.
```yaml
kubectl apply -f mysql/mysql-pv.yml
```
#### Verify Persistent Volume.
```yaml
kubectl get pv
```
#### 3 MySQL Resources Create Persistent Volume Claim.
```yaml
 kubectl apply -f mysql/mysql-pvc.yml
```
#### Verify Persistent Volume.
```yaml
kubectl get pvc
```
For detailed information about a specific PV
```yaml
kubectl describe pv -n wordpress-namespace
kubectl describe pvc my-claim -n wordpress-namespace
```
#### 4 Secrets (e.g., database username/password) .
```yaml
 kubectl apply -f mysql/mysql-secret.yml
```
#### 5 Configuration values (e.g., database settings) .
```yaml
 kubectl apply -f mysql/mysql-configmap.yml
```
#### 6 MySQL deployment .
```yaml
kubectl apply -f mysql/mysql-statefulset.yml
```
#### 7 MySQL service expose .
```yaml
kubectl apply -f mysql/mysql-service.yml
```
## WordPress Resources
#### 8 Persistent Volume create .
```yaml
kubectl apply -f wordpress/wordpress-pv.yml
```

#### 9 Persistent Volume Claim .
```yaml
kubectl apply -f wordpress/wordpress-pvc.yml
```
#### 11 Persistent Volume create .
```yaml
kubectl apply -f wordpress/wordpress-configmap.yml
```

#### 12 WordPress deployment create .
```yaml
kubectl apply -f wordpress/wordpress-deployment.yml
```

#### 13 WordPress service expose .
```yaml
 kubectl apply -f wordpress/wordpress-service.yml
```
#### 14 Autoscaling configure .
```yaml
 kubectl apply -f wordpress/wordpress-hpa.yml
```
#### 15 External traffic route .
```yaml
 kubectl apply -f wordpress/wordpress-ingress.yml
```
#### 15 Port Forwading for MySql Local System  .
```yaml
kubectl port-forward svc/mysql 3306:3306 -n wordpress-namespace &
```
#### 16 Port Forwading for MySql AWS  .
```yaml
kubectl port-forward svc/mysql-svc 3306:3306 -n wordpress-namespace --address="0.0.0.0/0"
```
#### 17 Port Forwading for WordPress Local System  .
```yaml
kubectl port-forward svc/wordpress 8180:80 -n wordpress-namespace &
```

#### 17 Port Forwading for WordPress AWS  .
```yaml
kubectl port-forward svc/wordpress-svc 8180:80 -n wordpress-namespace --address="0.0.0.0/0"
```
## Troubleshooting

- If the `kind create cluster` command fails, ensure that Docker is running properly on your machine.
- If `kubectl get pods` shows that the pods are not running, check the logs of the respective pods using:

## .htaccess to increase upload file size and PHP limits
```yaml
php_value upload_max_filesize 100M
php_value post_max_size 100M
php_value memory_limit 256M
php_value max_execution_time 300
```
## check all services running on single command 
```yaml
 kubectl get all -n wordpress-namespace
 kubectl get svc -n wordpress-namespace
```
#

# ArgoCD - GitOps Continuous Delivery for Kubernetes

## **What is ArgoCD?**
ArgoCD is a declarative, GitOps-based continuous delivery tool for Kubernetes. It automates the deployment and synchronization of applications and infrastructure configurations directly from a Git repository.

---

## **Why Use ArgoCD with Kubernetes?**

- **GitOps Workflow**: Ensures that your cluster state matches the source of truth in your Git repository.
- **Automated Syncing**: Automatically applies updates when changes are pushed to Git.
- **Visualization**: Provides a web-based UI and CLI to track application status and health.
- **Rollback Support**: Easily roll back to a previous version if needed.
- **Scalability**: Suitable for managing multiple clusters and applications.

---

## **How to Use ArgoCD with Kubernetes**

### **1. Install ArgoCD**
Deploy ArgoCD in your Kubernetes cluster using the following command:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
watch kubectl get pods -n argocd
```
### **2 Install ArgoCD CLI**
- Official Website Reference 
```bash
https://kostis-argo-cd.readthedocs.io/en/refresh-docs/getting_started/install_cli/
```
- Direct Commands
```bash
sudo curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.4.7/argocd-linux-amd64
sudo chmod +x /usr/local/bin/argocd
uname -m
argocd version
```
![image](https://github.com/user-attachments/assets/c14764c2-3844-4b1f-96ef-73f4a2f60d73)

### Verify the NodePort Service
```bash
kubectl get pods -n argocd
```
![image](https://github.com/user-attachments/assets/8e2ff0ac-9ffa-4079-a771-de0d96eb94f7)

### Change ArgoCD Server Service Type to NodePort
- Verify the NodePort Service
```bash
kubectl get svc -n argocd
```
![image](https://github.com/user-attachments/assets/2c52489a-9957-4e1f-b837-44430988102e)

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```
![image](https://github.com/user-attachments/assets/59fa4503-91fc-4811-8831-2d8611b6f50c)

### Port Forwarding Command 
```bash
kubectl port-forward service/argocd-server -n argocd 8100:443 &
```
![image](https://github.com/user-attachments/assets/e9bc112b-5982-4420-8435-00a56aec9f94)
![image](https://github.com/user-attachments/assets/610b88c9-3d1f-429b-837d-7e784eaccedb)

## Login to ArgoCD Using CLI
- username : admin
- password
```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```
![image](https://github.com/user-attachments/assets/6d2bd82c-bec8-4531-ac0f-dc31ecc255a0)

### Port Forwading issue debugging 
- error
![image](https://github.com/user-attachments/assets/a7772c8b-53af-462b-a2f0-3c0ee5a3f72a)
- solved 
![image](https://github.com/user-attachments/assets/9f688ed5-4e2d-4152-bacf-2435563dcc4a)

## Login ArgoCD username : admin | password : encrypted by kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
![image](https://github.com/user-attachments/assets/91b4fb22-d1c6-4d7b-b65e-c9045c434fa1)

#  FrontEnd Application Form 
## Create Application 
![image](https://github.com/user-attachments/assets/66333290-c3d5-46fb-b6f3-836f4da409da)

## Application Form Part 1 
![image](https://github.com/user-attachments/assets/173a750c-d21d-41ac-86fb-8821596401a1)

## Application Form Part 2
![image](https://github.com/user-attachments/assets/c23f6dfe-bb6c-425c-b4ad-846bae08093c)

## Application Form Part 3 Click in Create 
![image](https://github.com/user-attachments/assets/781aa940-28ac-4e16-bd2e-705987227c1c)
![image](https://github.com/user-attachments/assets/6a5d6b8e-c2b5-467d-8532-e3ebfb312af8)

# Backend Application Form 
## Form 1 
![image](https://github.com/user-attachments/assets/4ed5a39e-c74a-444e-a32b-09991b4959b6)

## Form 2 
![image](https://github.com/user-attachments/assets/824a03dd-9e21-4ecc-89f7-09f499d0a671)

## Final Result's 
![image](https://github.com/user-attachments/assets/3f749a1d-0e79-4977-992f-edc5abc9769a)
![image](https://github.com/user-attachments/assets/f2e28910-8cf3-474c-9c64-dca07b9a24e2)

# Jenkins
![image](https://github.com/user-attachments/assets/b1ea58a0-725d-4c8c-804a-1fe2d5844f53)

- Jenkins is an open-source automation server widely used for continuous integration (CI) and continuous delivery (CD). It automates repetitive tasks involved in the software development lifecycle, such as building, testing, and deploying applications. Jenkins is highly extensible and supports numerous plugins for integrating with various tools, repositories, and environments.
## Official Website : https://www.jenkins.io/
## Why Use Jenkins in Your WordPress Project? 
- For your WordPress project hosted on GitHub, Jenkins can play a vital role in automating the build, test, and deployment workflows. Here's how Jenkins can be used in your project
## 1. Continuous Integration (CI)
- Automate Code Testing: Jenkins can run unit tests or code linting tools whenever a developer pushes changes to the GitHub repository. This ensures code quality and prevents bugs from being introduced into the main branch.
- GitHub Integration: Jenkins can monitor the GitHub repository for changes (using webhooks or periodic polling). Whenever a new commit is pushed, Jenkins can automatically trigger a build or deployment pipeline.
## 2. Continuous Deployment (CD)
Automate Kubernetes Deployment: Your project uses Kubernetes manifests stored in the k8s-manifests directory. Jenkins can be configured to:
- Build and package updated images (e.g., WordPress and MySQL containers).
- Deploy the changes to your Kubernetes cluster (using kubectl or a tool like ArgoCD).
- Docker Image Builds: If you're building custom Docker images, Jenkins can automate the docker build and docker push process to push updated images to a container registry (like Docker Hub or AWS ECR).
# Jenkins Prerequisites

## Minimum Hardware Requirements
- **RAM**: 256 MB
- **Drive Space**: 1 GB  
  *(10 GB is a recommended minimum if running Jenkins as a Docker container)*

## Recommended Hardware Configuration (for a small team)
- **RAM**: 4 GB+
- **Drive Space**: 50 GB+

## Additional Notes
- Ensure sufficient CPU resources, especially if multiple pipelines or plugins are in use.
- If running Jenkins in a virtualized or containerized environment, allocate appropriate resources to avoid performance bottlenecks.
- For larger teams or complex pipelines, consider scaling resources further based on workload.

# Installing Commands 

## 1 Update System 
```bash
sudo apt-get update 
```
## 2 Install Java 
```bash
sudo apt install fontconfig openjdk-17-jre
java -version
```
## Jenkins Long Term Support release
```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```
```bash
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```
```bash
sudo apt-get update
sudo apt-get install jenkins
```
## Check Jenkins Status 
```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
## Open your browser and navigate to
```bash
http://<your-server-ip>:8080
```
## Retrieve the initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
![image](https://github.com/user-attachments/assets/5c7690cc-262b-4150-a3fb-396ce2c736ce)
![image](https://github.com/user-attachments/assets/ee5e312d-d3ef-43f4-948e-65f50fc83e36)
## Install Suggested Plugins
![image](https://github.com/user-attachments/assets/2732bc42-f1a5-487e-ab59-2f5ad1e79f7e)

## Create First Admin User
![image](https://github.com/user-attachments/assets/d0472be2-7853-4373-a19c-b2982dfef760)
## Login URL 
```bash
http://localhost:8080/
```
![image](https://github.com/user-attachments/assets/fda288f0-4639-4918-9e51-4ac1dd949087)

# Install Trivy "Jenkins"
```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update -y
sudo apt-get install trivy -y
```
## Install and configure SonarQube (Master machine) Add Ports If You are Working on AWS 
```bash
docker run -itd --name SonarQube-Server -p 9000:9000 sonarqube:lts-community
```
![image](https://github.com/user-attachments/assets/f29bc0ac-0d0a-40cb-96f2-5e5871e14faf)

![image](https://github.com/user-attachments/assets/846bd9f2-b9cb-4f5e-81d8-028aca327ffc)

![image](https://github.com/user-attachments/assets/db407eb3-00fd-4116-89fd-fcadf2739048)

## Sonar Container Error : When sonar server not running use this commands 
```bash
docker ps -a
```
```bash
➜  Desktop docker ps -a                                                                                            

CONTAINER ID   IMAGE                     COMMAND                  CREATED      STATUS                                PORTS                                       NAMES
c4a77427a48f   sonarqube:lts-community   "/opt/sonarqube/dock…"   2 days ago   Exited (255) Less than a second ago   0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   SonarQube-Server
bb30fc7c7dac   kindest/node:v1.31.2      "/usr/local/bin/entr…"   4 days ago   Up Less than a second                                                             wordpress-namespace-worker
29e7e514fd2b   kindest/node:v1.31.2      "/usr/local/bin/entr…"   4 days ago   Up Less than a second                                                             wordpress-namespace-worker2
35952a83e257   kindest/node:v1.31.2      "/usr/local/bin/entr…"   4 days ago   Up Less than a second                 127.0.0.1:42253->6443/tcp                   wordpress-namespace-control-plane
➜  Desktop docker start c4a77427a48f

```

# Setup Jenkins Email Notification
## Step 1 Gmail Settings 
![image](https://github.com/user-attachments/assets/138361d1-d5ad-41dc-8d06-98f09ab04dd1)
- Click on Security on two step verification 
- ![image](https://github.com/user-attachments/assets/b8215da8-6fa1-43ef-9d80-fa785bc803c1)
- ![image](https://github.com/user-attachments/assets/e3a2bc73-fcdb-46bc-9775-697bad934505)
- Search APP Password
- ![image](https://github.com/user-attachments/assets/bc963737-aac1-42ec-855a-45cac4f94711)

## Configure credentials
![image](https://github.com/user-attachments/assets/acb8f0d0-9157-4b85-a573-9e41c438c145)
## Click on 
![image](https://github.com/user-attachments/assets/11bb93b4-03c4-4893-ab0e-8c3ec47d6d2f)
## Add credentials
![image](https://github.com/user-attachments/assets/c3cf92c3-ea30-46de-be34-e4514a078ca5)
- Result ![image](https://github.com/user-attachments/assets/9e4dc4d6-ac35-4ac7-92ab-895cfde93968)
## Manager Jenkins 
![image](https://github.com/user-attachments/assets/47cd0230-f4ee-40cd-98bd-567648c96ed8)
## Configure Extended E-mail Notification
![image](https://github.com/user-attachments/assets/88c5ccb5-41f8-4a9f-9c54-e820ab984436)
## Advance 
![image](https://github.com/user-attachments/assets/83ef20cc-9d7a-4892-bb39-8034b2ab1c9c)
## Test Check Email Option Working or not 
![image](https://github.com/user-attachments/assets/568e0a0d-04a3-4c95-802f-af1d7dd49d69)
![image](https://github.com/user-attachments/assets/d9233f4b-3acc-45d4-bdae-8bfdf0aa88b7)

# Setup OWASP ..!
## Go to Jenkins Master and click on Manage Jenkins --> Plugins --> Available plugins install the below plugins:
- OWASP
- SonarQube Scanner
- Docker
- Pipeline: Stage View

## Dependency-Check 
![image](https://github.com/user-attachments/assets/031d9579-0682-43db-9f84-af5abfe6a545)


## OWASP Dependency-Check 
![image](https://github.com/user-attachments/assets/35ddfc8c-63e0-4af1-bb71-1727fa4bfed7)

## Docker Install 
![image](https://github.com/user-attachments/assets/bf4bce7b-2bfe-4610-8a20-196a14bb6235)

## Dependency-Check installations
![image](https://github.com/user-attachments/assets/d5320be9-1eaa-44e2-825c-d8ff68fdc61e)

## Jenkins & SonarQube | Administration > Users
![image](https://github.com/user-attachments/assets/a464f913-c265-4746-b965-caf4bcecc290)

## Create Jenkins Token for Access SonarQube in Jenkins Generat Token 
![image](https://github.com/user-attachments/assets/e703350b-c1bd-49f3-8a8a-d289a0fa77af)

## Sonar Add credentials
![image](https://github.com/user-attachments/assets/2760c609-df75-48a7-8bef-d7bfb89767cc)

## Two Credentials added on jenkins 
![image](https://github.com/user-attachments/assets/653382ce-9436-428a-abba-25b136a98455)

## Add SonarOube Server on Jenkins 
![image](https://github.com/user-attachments/assets/8b5624d9-410e-432a-9554-4f01230d04ea)

## Add GitHub on Jenkins 
- Settings > Developer Settings > New personal access token (classic) | Click on Generat Tokens 
![image](https://github.com/user-attachments/assets/33ee2dc2-81ee-457a-a495-4c9795fe09a7)
![image](https://github.com/user-attachments/assets/818c3ccd-48f6-4b1b-943f-8429f12f16bb)

## Add SonarQube Installations 
![image](https://github.com/user-attachments/assets/e2bcb3be-0eaa-406e-a0bd-ea482945d2bd)

## 
## WebHook For SonarQube 
- Whenever Jenkins hit on SonarQube they need WebHooks
- Sonar > Configuration > WebHooks SonarQube does not allow the use of localhost in webhook URLs because these addresses are inaccessible to other services outside the server itself. However, since your goal is to test the webhook on your local system,
  
![image](https://github.com/user-attachments/assets/09b7d6f3-13c7-4cbb-8653-b30a8d60476f)

## Global Untrusted Pipeline Libraries | Manage Jenkins > System 
- Sharable libraries available to any Pipeline jobs running on this system. These libraries will be untrusted, meaning they run with “sandbox” restrictions and cannot use @Grab.
![image](https://github.com/user-attachments/assets/14114942-a5df-47bb-9596-4868a610f8d2)
![image](https://github.com/user-attachments/assets/1ddc7bda-5d98-4ebb-9109-7615b97adbe0)
- Save 

## Groovy Scripts already added on github : https://github.com/Burhan1009/Jenkins_Automate_libraries.git
![image](https://github.com/user-attachments/assets/5e245b02-fb2f-4d6e-be53-f09caa381dd8)

## Add Docker Credentials for Push a Docker Image
![image](https://github.com/user-attachments/assets/01624d0f-30e7-439f-b12e-755d28afccc3)

- Docker Credentials add on Jenkins
![Screenshot from 2025-01-19 19-26-27](https://github.com/user-attachments/assets/f1f85c44-0dab-40e2-99d1-cc7ecddf532a)

- All Credentials
- ![Screenshot from 2025-01-19 19-28-58](https://github.com/user-attachments/assets/ea36d19c-635d-48fa-b786-db0d3993d773)

## Jenkins CI Configuration 
- This is the CI pipeline for WordPress 
![Screenshot from 2025-01-19 19-48-15](https://github.com/user-attachments/assets/8709d364-410c-4e39-850d-468c0c415b1a)
- Configuration General
![Screenshot from 2025-01-19 19-49-20](https://github.com/user-attachments/assets/52d31322-5297-4612-b234-802bf29dd867)

- Add GitHub credentials
![image](https://github.com/user-attachments/assets/9627998d-dd3f-4625-945f-8b4eae525e2e)

- Pipeline script
![image](https://github.com/user-attachments/assets/469eaf4f-7c90-4bc6-bca8-4cb8e8739e89)

## WordPress CD Pipeline :- https://shorturl.at/HuEsb
![image](https://github.com/user-attachments/assets/61d96a42-4bd2-495d-a375-41d52ef464c9)

