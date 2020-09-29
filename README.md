# CapstoneProject

Here, I am doing a ROLLING DEPLOYMENT, where I am deploying the HTML App (index.html) to an EKS cluster using Docker Image deployment. Here, I am sharing complete steps to follow the same.

GitHub Repository Link -> https://github.com/shahmayank/CapstoneProject

GiHube Repository Details:
--------------------------
1. Jenkinsfile
2. Dockerfile
3. index.html
4. Snapshots Folder -> It contains all the screenshots required for Project
5. ClusterNetworking.yml -> Created by eksctl
6. ClusterNodeInfra.yml -> created by eksctl
7. Deployment.yml -> Deployment Manifest
8. Services.yml -> Services Manifest
9. Capstone Project Review Snapshot Document -> It contains snapshot attached and their descriptions available
10. README.MD

Steps to set-up Jenkins in the Unix Server:
-------------------------------------------
1. Update existing packages -> 
sudo apt-get update

2. Install Java -> 
sudo apt install -y default-jdk

3. Download Jenkins package -> 
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

4. Add the following entry in your /etc/apt/sources.list ->
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

5. Update your local package index ->
sudo apt-get update

6. Install Jenkins ->
sudo apt-get install -y jenkins

7. Start the Jenkins server ->
sudo systemctl start jenkins

8. Enable the service to load during boot ->
sudo systemctl enable jenkins
sudo systemctl status jenkins

9. Jenkins plugins needed for pipelining -> 
Blue Ocean
Blue Ocean Executor Info
Blue Ocean Pipeline Editor
Config API for Blue Ocean
Display URL for Blue Ocean
Events API for Blue Ocean
Git Pipeline for Blue Ocean
GitHub Pipeline for Blue Ocean
Pipeline implementation for Blue Ocean
Pipeline: AWS Steps
aws codepipeline 
cloudbees aws credentials 


Steps to set-up Docker, Kubectl & Eksctl in the Unix Server:
------------------------------------------------------------
1. Install AWS CLI -> 
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt-get update 
sudo apt-get install unzip 
unzip awscliv2.zip
sudo ./aws/install

2. Configure AWS CLI ->
aws configure

3. Install Docker -> 
sudo apt-get install docker.io

4. Create a docker file in current direcotry with below parameters ->
FROM nginx:alpine
COPY . index.html /usr/share/nginx/html/

5. Make Docker Image -> 
docker build -t my-app .

6. Run Docker Image -> 
docker run -p 8000:80 my-app 

7. Create ECR Registry and upload Image -> 
(To deal with access issues run below command)
sudo usermod -a -G docker $USER
newgrp docker

8. Install  eksctl (https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html) ->
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

9. Install kuvbectl ->
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.9/2020-08-04/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile
kubectl version --short --client


10. Create a Cluster & its Infrastructure ->
eksctl create cluster --name peyush-cluster --version 1.17 --region us-west-2 --nodegroup-name mayank-nodes --node-type t3.small --nodes-min 1 --nodes-max 1 --ssh-access --ssh-public-key DockerMayank --managed

11. Create Kube Config ->
aws eks --region us-west-2 update-kubeconfig --name mayank-cluster

12. Create & Apply Deployment & Services YML ->  
kubectl apply -f deployment.yaml
kubectl apply -f services.yaml

13. Delete a Cluster -> 
eksctl delete cluster -n peyush-cluster -w

14. To deal with jenkins access issues with docker use below commands
sudo groupadd docker
sudo usermod -aG docker jenkins
And then REBOOT Jenkins