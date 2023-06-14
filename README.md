# Cloud Native Monitoring Application

# Project Overview And High Level Design

Let's start with the High Level Design.

The diagram below provides a visual representation of the services used in this project and how they are connected. This application uses Python, Docker, DockerImage, Elastic Container Registry(ECR), Elastic Kubernetes Service(EKS) and Kubernetes itself.

As we go through the project, we will discuss the services in detail and point to resources that will help you get up to speed with them.

![Aws file](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/a905c359-4657-4fd4-965a-ff4e7ed59c14)

What Do We Need?

 - Python : A language to create a monitoring application using it's modules like Flask and Psutil. 
 
 - Docker : To create the containerised application using Docker. Build the DockerImage out of Dockerfile and running the Docker container locally using Docker Commands.
 
 - Elastic Container Registry : To push the docker image to ECR which will store the image, and we can retrieve and use the DockerImage in secure and efficient manner.
 
 - Elastic Kubernetes Service : To create a nodes and deploy the application on kubernetes
 
 - Kubernetes : To kubectl the commands to access our pods and deployment.
