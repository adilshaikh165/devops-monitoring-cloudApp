# Cloud Native Monitoring Application

# Project Overview And High Level Design

Let's start with the High Level Design.

The diagram below provides a visual representation of the services used in this project and how they are connected. This application uses Python, Docker, DockerImage, Elastic Container Registry(ECR), Elastic Kubernetes Service(EKS) and Kubernetes itself.

As we go through the project, we will discuss the services in detail and point to resources that will help you get up to speed with them.

![Aws file](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/a905c359-4657-4fd4-965a-ff4e7ed59c14)

# Blog post for step by step Demonstration!

https://adilshaikh165.hashnode.dev/cloud-native-monitoring-application

## Prerequisites!

- AWS Account

- Programmatic access and AWS configured with CLI

- Python3 Installed

- Docker and Kubectl Installed

- Code editor

## Create a monitoring application using Flask ✨

Step 1. Deploy the Flask App locally by clonning the Repository.

```bash
git clone https://github.com/adilshaikh165/devops-monitoring-cloudApp
```

Step 2. Install Dependencies.
The requirements.txt file will contain all the necessay modules to be installed for running the app on your machine.

You can use the following command to install all the requirements:
```bash
pip3 install -r requirements.txt
```
To run app locally on "localhost:5000" you need to run the mentioned command:
```bash
python3 app.py
```
The Application will look like this when it's successfully run on your local machine 

![customized](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/a0506077-81c8-4772-a71e-1feba7358314)

## Dockerised The Application

Step 1. To Dockerised the Application you need to create the DockerFile.
The Python base image used in the Docker file is "python:3.9-slim-buster".


Step 2. Create the DockerImage out of the Docker File using "build" command.
```bash
docker build -t my-flask-app .
```

Step 3. Create the DockerContainer using DockerImage using "run" command.
```bash
docker run -p 5000:5000 {image_id} 
```

Using this command the Container will work and Application will run on your "localhost:5000" on your Docker Container.So now you'll get the CPU and MEMORY of you Docker Container that is running.

## Creating the ECR(Elastic Container Registry) using Python Boto3 and pushing Docker Image to ECR.

We'll create the ECR repository in VSCode itself using Python and Boto3 module

Key Point : Boto3 makes it easy to integrate your Python application, library, or script with AWS services including Amazon S3, Amazon EC2, Amazon DynamoDB, and more.

Step 1. You need to create a "ecr.py" file to use boto3 to integrate with our ECR and other AWS Services.

Run the "ecr.py" code using command:
```bash
python3 ecr.py
```

Once the ecr.py file is run successfully using the above command you'll get your Repository Uri which is been created in your AWS ECR.

Step 2. Push the DockerImage to ECR

Run the command's which are used to push the image. You can view those command in "View push command" section.Kindly refer the push command from your ECR repository.

Step 3. Once you run all the commands in proper order the DockerImage will be pushed into your ECR and you can view it under the "image" tab

![Latest Image](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/a643ed04-cf71-4a4c-88b8-9e56a395cd84)

Hence uptil now the DockerImage is successfully pushed into you ECR.


## Creating the EKS cluster and Node Groups

You need to create a EKS Cluster and Nodes group in order to deploy your container in form of Kubernetes container.

Step 1. Create a EKS Cluster of your own and make sure to create a new Security Group with Port 5000 enabled.

Make sure to select the security group you created in step 2 while configuring your EKS Cluster.

Step 2. Create a Node Group with 2 nodes having Instance of tyoe "t2.micro".

## Creating the Deployment and Service for kubernetes deployment.

Step 1. Create the Deployment file

Step 2. Create The Service 

Run the "eks.py" file containing the deployment and service in default namespace using command :
```bash
python3 eks.py
```

#### Important : In order for authentication for the image which is on ECR private registry you need to create the "secret" file inside the k8s cluster which have necessary information regarding your EKS cluster for authentication purpose. And you need to call the secret file using the following block of code in your deployment file or else the Container will show "ImagePullBackOff" error.
```bash
image_pull_secrets=[
                    client.V1LocalObjectReference(name="<your-secret-file-name>")
]
```

Once you have authenticated with your Private Registry you can see the Container creating by using the following command :

```bash
kubectl get pods -n default -w

kubectl get deployment -n default

kubectl get svc -n default
```

-n : To indicate default namespace

-w : To view in watch mode

You can refer the below snapshot of terminal for verifying your kubectl commands

![image](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/b50d1add-75a9-487e-9cad-28b7385d568e)

Once your pod is up and running, run the port-forward to expose the service using the command 

```bash
kubectl port-forward svc/{your-service-name} 5000:5000
```
And now if you upen your Localhost then you'll see the Application successfully hosted on Kubernetes



























