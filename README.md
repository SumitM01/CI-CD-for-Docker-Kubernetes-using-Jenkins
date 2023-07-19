# CI-CD-for-Docker-Kubernetes-using-Jenkins
This project contains documented details of implementation of a CI CD pipeline using Jenkins for Docker and Kubernetes
## Overview
The project focuses on implementing continuous delivery for Docker containers 🐳. The aim is to continuously build Docker images and deploy them to a Kubernetes cluster 🚀. This approach is commonly used in microservice architecture, but it can be applied anywhere containers are used.

With continuous code changes, there needs to be a continuous build and test process 🔨, as well as regular deployment of the containers 🚛. The deployment process is typically handled by the operations team 👷‍♂️, who manage the container orchestration tool like Kubernetes 🌐. However, manual deployment can create dependencies 🔗 and be time-consuming ⏰.

To address this, the project aims to automate the build and release process of container images, allowing for fast and continuous deployment as soon as code changes are made by developers 💻. This will be achieved through the implementation of a continuous delivery or deployment pipeline for Docker containers 📦.

## Services used
<div width="50%">
<a href="https://jenkins.io/"><img src="https://logowik.com/content/uploads/images/jenkins8460.jpg" height="80px" alt="Jenkins"></a>
<a href="sonarqube.org"><img src="https://1.bp.blogspot.com/-zYP58acwgq8/X_2nBaX8ESI/AAAAAAAAAIg/T8Jzb-Pfr00_9rIYpaa6DECiNHmg6S-sgCLcBGAsYHQ/s320/sonarqube-logo-square-small.png" height="80px" alt="Sonarqube Scanner"></a>
<a href="https://maven.apache.org/"><img src="https://logowik.com/content/uploads/images/maven-apache3537.jpg" height="80px" alt="Maven"></a>
<a href="https://www.docker.com/"><img src="https://logowik.com/content/uploads/images/301_docker.jpg" height="80px" alt="Docker"></a>
<a href="https://kubernetes.io/"><img src="https://logowik.com/content/uploads/images/kubernetes5574.jpg" height="80px" alt="Kubernetes"></a>
<a href="https://helm.sh/"><img src="https://logowik.com/content/uploads/images/helm6256.jpg" height="80px" alt="Helm"></a>
<a href="https://aws.amazon.com/ec2/"><img src="https://seeklogo.com/images/A/aws-ec2-elastic-compute-cloud-logo-2F9E73DBA5-seeklogo.com.png" height="80px" alt="AWS EC2"></a>
<a href="https://aws.amazon.com/s3/"><img src="https://logowik.com/content/uploads/images/amazon-s37765.jpg" height="80px" alt="AWS S3"></a>
<a href="https://aws.amazon.com/route53/"><img src="https://seeklogo.com/images/A/aws-route-53-logo-EEB0D14819-seeklogo.com.png" height="80px" alt="AWS Route53"></a>
<a href="https://aws.amazon.com/elasticloadbalancing/"><img src="https://seeklogo.com/images/A/aws-elastic-load-balancing-logo-73F2ABF601-seeklogo.com.png" height="80px" alt="AWS ELB"></a>
<a href="https://aws.amazon.com/iam/"><img src="https://seeklogo.com/images/A/aws-iam-identity-and-access-management-logo-99B1CACF5A-seeklogo.com.png" height="80px" alt="AWS IAM"></a>
<a href="https://github.com/"><img src="https://seeklogo.com/images/G/github-logo-5F384D0265-seeklogo.com.png" height="80px" alt="Github"></a>
</div>

## Project Architecture
The following events happen serially:

- A developer makes a code change and pushes it to GitHub 💻.
- Jenkins fetches the code, including the Dockerfile, Jenkinsfile, and Helm charts 📥.
- The code is tested and analyzed using Checkstyle and SonarQube scanner 🔍, with results uploaded to SonarQube Server 📈.
- If the code passes all quality gates, an artifact is built with Maven 🔨.
- A Docker build process starts to build the Docker image 🐳.
- If everything passes, the Docker image is pushed to Docker Hub 🚀.
- Jenkins uses Helm to deploy the Helm charts to the Kubernetes Cluster 🌐.
- The Helm chart deployment creates all necessary resources, such as pods, services, secrets, and volumes 📦.
- If any changes are made, such as a new image tag for an application pod, they are implemented 🔧.

## Implementation Details

### Continous Integration Setup 🛠️
Follow the README.md file in https://github.com/SumitM01/CI-using-Jenkins–Nexus-and-Sonaqube repository to create and setup the Continous Integration pipeline. Create instances for Jenkins and Sonarqube scanner only. Do not create an instance for Nexus artifact storage as it is not required.

### Setup Jenkins server
- Install these additional plugins on Jenkins.
    - Docker pipeline 🐳
- Log in to jenkins instance using SSH and install openjdk-11-jdk and openjdk-8-jdk using the following commands 🔧
```bash
sudo apt update
sudo apt install openjdk-8-jdk -y
sudo apt install openjdk-11-jdk -y
```
![configure-jenkins-maven](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/884b5ddd-6ed8-48fa-a9b3-fc41e48b7d2a)
![configure-jenkins-credentials](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/e2d2e75f-a17f-4fb6-b619-dad633323ef3)

- Configure JDK installation on Jenkins by providing Java_Home path.
![configure-jenkins-jdk-8](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/7c0cc15a-5a01-4476-8fca-0cea82c00356)
![configure-jenkins-jdk-11](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/54c88bc9-9d57-4143-b480-9365e66dc497)

- Configure Sonarqube scanner and sonarqube server with sonarqube token 🔍
![configure-jenkins-sonarqube-scanner](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/3d6dc20b-274d-4864-9f7c-c31d535d671e)
![configure-jenkins-system-sonarqube-server](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/74f8b9b9-d0dc-42ca-864c-e9a55e6dd44f)

- SSH to the instance and install docker engine in it using the following commands 🐳
```bash
#!/bin/bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg -y
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg -y
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
"deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
"$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```
- Add Jenkins user to Docker group 🔧
```bash
usermod -aG docker jenkins
```

### Complete kOps pre-requisites
- Create a domain in GoDaddy and a subdomain in Route 53.🌐
    - Create a Domain on GoDaddy like this.🖱️
  ![domain-created](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/d0104093-b08f-464c-b23f-07f6e20db5f9)

    - Create a sub-domain/hosted zone on AWS Route 53 like this.🖱️
 ![sub-domain-created](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/8fa21120-8e2b-4021-83c6-33bfbd2488d6)

- Copy the NameServer records to Godaddy DNS manager 📋 from the subdomain.
    - After creation of hosted zone, copy the displayed records 📋.
  ![sub-domain-ns-recs](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/510fc11d-496b-4ce7-a860-c8ef7ab1840a)

    - **On Godaddy DNS Manager**
        - Under *Nameservers* section, click on *Change Numservers* and paste the copied records individually.
    ![domain-ns-recs-replaced](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/f0612bcd-aa8a-421f-a591-e02c20d708fe)

- Launch the kOps server instance with the following specifications 🚀:
    - *AMI*: Ubuntu 20/18
    - *Instance type/*: t2.micro
    - *Security Group Inbound Rules*: 22 allowed from your IP
![kops-server-created](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/d1c7e521-8cac-4a81-9b8f-80dd2619fd29)

- Create an S3 bucket 🪣 on the same region as the server.
    - During creation of the bucket ensure the bucket is in the same zone as the kops server.
![s3-bucket-created](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/cbaa6172-9ada-431b-9921-6cdf17c1e9a7)

- Create an IAM 🔐 role for using awscli and store its credentials.
![iam-user-created](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/0c1f1212-cb9f-4b78-a6ee-a37f5b30a327)

- Install awscli on kOps server and configure it with the IAM credentials 🔐.
![configure-awscli](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/e87ae1db-2764-44fb-a43f-eb18e54e12e2)

- Run the following command to download ⬇ awscli :
```bash
apt get update
apt install awscli -y
```
- Install kubectl and kops from the kubernetes site 🌐:
    - Install kubectl using the following command 🔧:
    ```bash
     curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    ```
    - Install kOps from Kubernetes site using the following command 🔧:
    ```bash
    curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
    ```
    
### Configure SSH key login to github remote:
- Generate the ssh keys on the kops server using ssh-keygen 🔑
- Go to account settings -> SSH and GPG keys -> add key -> paste the contents of the public ssh key -> save 💾
![git-ssh-keys-created](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/65b17e78-dde6-4bba-9360-b2d28b0181a2)

### Create a separate repository for the project 📁:
- Clone the created repo into your kops machine using SSH link 🔗.
```bash
git clone git@github.com:git_username/git_repository_name.git
```
- ***IMPORTANT***: Cloning the repository on the machine validates the authentication using the created SSH keys🔑.
- Copy the contents 📋 of the vprofile-project/cicd-kube branch.
- Clone 🔗 the vprofile-project repo onto the kops machine.
```bash
git clone https://github.com/devopshydclub/vprofile-project.git
```
- Checkout to cicd-kube branch🔀.
```bash
git checkout cicd-kube
```
- Copy all the files in the root to the created repository folder 📋.
```bash
cp -r * ../your_created_repo/ 
```
- Delete files inside your created repo that are not required: docker-db, docker-web, ansible,compose 🗑️.
```bash
rm -rf docker-db docker-web ansible compose
```
- Copy dockerfile from inside docker-app folder to root and delete dockerapp folder 📋.
```bash
cp Dockerapp/Dockerfile .
rm -rf Dockerapp
```
- Delete 🗑️ contents 📋 inside helm/vprofilecharts/templates folder 📁 and replace them with contents of Deploying-an-application-on-Kubernetes-    cluster/Setupfiles folder📁.
```bash
cd helm/vprofilecharts/templates
rm -rf
cd 
git clone https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster.git
cp -r Deploying-an-application-on-Kubernetes-cluster/Setupfiles/* your_created_repo/helm/vprofilecharts/templates/
```
### Create an EC2 volume and configure it 💽:
- Create an EC2 volume using the following command🔧:
```bash
aws ec2 create-volume --availability-zone=your_prefered_zone --size=3 --volume-type=gp2
```
![ec2-volume-created](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/eddbd266-e9ac-471e-91c9-4462cd424c68)

- Note down the volume ID as displayed after volume creation📝.
- Specify the volume ID in the vprodbdep.yml file with the copied ID 🔧.
- **On AWS console**
    - Go to EC2 management console🖱️.
    - On Navigation Pane, go to volumes 🖱️.
    - Search for the created volume with the volume ID and select it🔍.
    - Click on Manage tags and add the following tag to it🔧:
        - Key : KubernetesCluster
        - Value : your_subdomain_name
        - ***IMPORTANT:*** This is necessary because without the cluster tags the volume won't get attached to the required instance for database purposes⚡.
      

### Create Kubernetes Cluster using kops🌐:
- Run the following command to create a Kubernetes cluster using kOps:🚀
```bash
kops create cluster --name=your_subdomain_name --state=s3://your_bucket_name --zones=your_preferred_zone --node-count=2 --node-size=t2.small --master-size=t3.medium --dns-zone=your_subdomain_name
```
### Launch Kubernetes cluster using kops:🚢
- Run the following command to launch the created cluster using kOps:🚀
```bash
kops update cluster --name vprofile.sumitmishra.info --state=s3://vprofile-kube-project --yes --admin
```
- Wait for 10-15 minutes for the cluster to launch fully.⏳

### Install Helm on the instance☸️
- While you wait for the cluster to be launched, you can install Helm on the kops server using the following commands
```bash
cd
wget https://get.helm.sh/helm-v3.12.2-linux-amd64.tar.gz 
tar -zxvf helm-v3.12.2-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
helm --help
```
![Installing-helm](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/5979710e-aba6-4847-902f-5a86e561bb8c)

### Check health of the cluster using kops:
- Run the following command to validate the created cluster using kOps:✅
```bash
kops validate cluster --name=vprofile.sumitmishra.info --state=s3://vprofile-kube-project
```
![cluster-validation](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/c589e62c-87dc-4404-93c1-09dc4433d064)

### Configure nodes of kubernetes cluster:⚙️
- Run the following command to taint all nodes to be Scheduled on launch.
```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane:NoSchedule-
```
- ***IMPORTANT:*** This is very important because, the latest kubernetes versions do not allow scheduling to be done on control nodes which causes various errors during deployment, to avoid that we should allow scheduling on the control node.
- Run the following command to check whether all nodes have the same zone.⚡
```bash
kubectl get nodes -L zone
```
- If there are no records under Zone then assign zones to individual nodes by running the following command for each node.
```bash
kubectl label nodes <node-name> zone=your_prefered_zone
```
- ***IMPORTANT:*** This is necessary because the node should be in the same zone as the created volume in order to get attached to it and in the same zone as specified in the deployment files in order to not raise an error during deployment.⚡

### Configure Node on Jenkins and kops server: 🖥️
- **On kops server**
- Connect to kops-server using SSH client.
- Using ubuntu user, install open-jdk-11 in the server.📦
```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```
- Create a folder as /opt/jenkins-slave and provide ownership of jenkins-slave to ubuntu user.📁
```bash
sudo mkdir /opt/jenkins-slave
sudo chown ubuntu.ubuntu /opt/jenkins-slave
```
- **On Jenkins server**
- Configure a node with the following settings:⚙️
    - Remote root directory : /opt/jenkins-slave
    - Labels:KOPS
    - Usage: only build jobs with label expressions matching this node
    - Launch method:launch agents via ssh
    - Host: private kops IP
    - Credentials: kops instance private login key
    - Host key verification strategy: non verifying verification strategy
    - Availability: keep this agent online as much as possible

### Write the Jenkinsfile ✍️
- **On local_machine**
- Write a Jenkinsfile inside your-created-repository by referring to the Jenkinsfile present in vprofile-project/cicd-kube directory.
- Push the contents to github remote repo.

### Configure and Run the pipeline:🚀
- **On jenkins**
    - Create a pipeline
    - Choose Poll SCM and provide * * * * * 
    - Choose Pipeline script from SCM and provide your github repository, branch and Jenkinsfile path then save.💾
    - Now commit to the repository then see that the pipeline gets automatically triggered after the commit.🚨
    - Wait for the pipeline to be completed successfully.✅
![Pipeline-success](https://github.com/SumitM01/CI-CD-for-Docker-Kubernetes-using-Jenkins/assets/65524561/89887083-1ba6-4395-8515-267371ead5c6)


- After the successful completion of the pipeline do the following
### Create a Route53 record 📝
- **On kops server**
- SSH to kops-server. 
- Run the following command to list all the running services in the project.📋
```bash
kubectl get svc
```
- Copy the Load balancer ARN from the displayed services.
- Create a new record in the hosted zone of route 53 with the value as the dns of load balancer.🌐

## Results🎉
After everything is done, wait for 5-10 mins⏳ then validate the services by accessing the website using the URL.🔗
- We can see that the website is up and the frontend services are running fine.
**Login Page**
  ![login-page](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/bf3251d9-50da-4203-be7a-767d3a47d98c)

**Welcome Page**
  ![welcome-page](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/25627cee-dcd4-4c03-9915-2b1695592b06)


- Here we can see that the backend services have been created and configured and are also running fine.
**User details Page (Database Validation)**
  ![db-validation](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/2970b6b7-2336-429f-a20d-21c322864d01)

**User details Page (Cache Validation)**
![cache-validation](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/1b129f62-1ce8-4613-a41c-d5a580ee3194)

**Rabbitmq status Page**
  ![rabbitmq-page](https://github.com/SumitM01/Deploying-an-application-on-Kubernetes-cluster/assets/65524561/6f7ec5e4-843b-412d-851a-a17d4841f20f)
  
## Cleanup🧹
- Cleanup the services one by one.
- Delete the cluster in the kops vm using the following command
```bash
kops delete cluster --name your_subdomain_name --state=s3://your_bucket_name --yes
```
- Take a snapshot of the entire stack and store it in an s3 bucket for future use.🔮
- Poweroff/terminate the instances
- Delete security groups.🗑️
- Delete S3 buckets if you don't require them.🗑️
- Delete the hosted zone on AWS Route53 if not required.🗑️

## Conclusion
This project implmented the complete Continous Integration and Continous Deployment pipeline using Jenkins for production deployment on Docker and Kubernetes cluster. This ensures efficient and streamlined development process and maintenance of the application.  

As documented in this README file, I have invested MANY MANY HOURS of my time in **researching 🔎, learning 📖, debugging 👨‍💻** to implement this project. If you appreciate this document please give it a ⭐, share with friends and do give it a try. Thank you for reading this! 😊

## References
- https://github.com/devopshydclub/vprofile-project/tree/cicd-kube
- https://www.udemy.com/course/decodingdevops
- https://Kubernetes.io
- https://hub.docker.com
- https://dcc.godaddy.com
- https://google.com
