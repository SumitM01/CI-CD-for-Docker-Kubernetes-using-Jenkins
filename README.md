# CI-CD-for-Docker-Kubernetes-using-Jenkins
This project contains documented details of implementation of a CI CD pipeline using Jenkins for Docker and Kubernetes

## Overview
The project focuses on **implementing continuous delivery for Docker containers**. The aim is to **continuously build Docker images** and **deploy them to a Kubernetes cluster**. This approach is commonly used in microservice architecture, but it can be applied anywhere containers are used.

With continuous code changes, there needs to be a **continuous build and test process**, as well as **regular deployment of the containers**. The deployment process is typically handled by the **operations team**, who manage the container orchestration tool like **Kubernetes**. However, manual deployment can **create dependencies** and be **time-consuming**.

To address this, the project aims to **automate the build and release process** of container images, allowing for fast and continuous deployment as soon as code changes are made by developers. This will be achieved through the implementation of a **continuous delivery or deployment pipeline for Docker containers**.

## Services used
<div width="50%">
<img src="https://logowik.com/content/uploads/images/jenkins8460.jpg" height="40px" alt="Jenkins">
<img src="https://1.bp.blogspot.com/-zYP58acwgq8/X_2nBaX8ESI/AAAAAAAAAIg/T8Jzb-Pfr00_9rIYpaa6DECiNHmg6S-sgCLcBGAsYHQ/s320/sonarqube-logo-square-small.png" height="40px" alt="Sonarqube Scanner">
<img src="https://logowik.com/content/uploads/images/maven-apache3537.jpg" height="40px" alt="Maven">
<img src="https://logowik.com/content/uploads/images/301_docker.jpg" height="40px" alt="Docker">
<img src="https://logowik.com/content/uploads/images/kubernetes5574.jpg" height="40px" alt="Kubernetes">
<img src="https://logowik.com/content/uploads/images/helm6256.jpg" height="40px" alt="Helm">
<img src="https://seeklogo.com/images/A/aws-ec2-elastic-compute-cloud-logo-2F9E73DBA5-seeklogo.com.png" height="40px" alt="AWS EC2">
<img src="https://logowik.com/content/uploads/images/amazon-s37765.jpg" height="40px" alt="AWS S3">
<img src="https://seeklogo.com/images/A/aws-route-53-logo-EEB0D14819-seeklogo.com.png" height="40px" alt="AWS Route53">
<img src="https://seeklogo.com/images/A/aws-elastic-load-balancing-logo-73F2ABF601-seeklogo.com.png" height="40px" alt="AWS ELB">
<img src="https://seeklogo.com/images/A/aws-iam-identity-and-access-management-logo-99B1CACF5A-seeklogo.com.png" height="40px" alt="AWS IAM">
<img src="https://seeklogo.com/images/G/github-logo-5F384D0265-seeklogo.com.png" height="40px" alt="Github">
</div>

## Project Architecture

The following events happen serially as depicted by the image:
- A developer makes a code change and pushes it to GitHub.
- Jenkins fetches the code, including the Dockerfile, Jenkinsfile, and Helm charts.
- The code is tested and analyzed using Checkstyle and SonarQube scanner, with results uploaded to SonarQube Server.
- If the code passes all quality gates, an artifact is built with Maven.
- A Docker build process starts to build the Docker image.
- If everything passes, the Docker image is pushed to Docker Hub.
- Jenkins uses Helm to deploy the Helm charts to the Kubernetes Cluster.
- The Helm chart deployment creates all necessary resources, such as pods, services, secrets, and volumes.
- If any changes are made, such as a new image tag for an application pod, they are implemented.

## Implementation Details

### Continous Integration Setup
Follow the README.md file in https://github.com/SumitM01/CI-using-Jenkins--Nexus-and-Sonaqube repository to create and setup the Continous Integration pipeline. Create instances for Jenkins and Sonarqube scanner only. Do not create an instance for Nexus artifact storage as it is not required.

## Results

## Conclusion

## References
