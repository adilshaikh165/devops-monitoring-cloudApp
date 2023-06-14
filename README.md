# Cloud Native Monitoring Application

# Project Overview And High Level Design

Let's start with the High Level Design.

The diagram below provides a visual representation of the services used in this project and how they are connected. This application uses Python, Docker, DockerImage, Elastic Container Registry(ECR), Elastic Kubernetes Service(EKS) and Kubernetes itself.

As we go through the project, we will discuss the services in detail and point to resources that will help you get up to speed with them.

![Aws file](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/a905c359-4657-4fd4-965a-ff4e7ed59c14)

What Do We Need?

 - Python : A language to create a monitoring application using it's modules like Flask and Psutil. 
 
 - Docker : To create the containerised application using Docker. Build the DockerImage out of Dockerfile and running the Docker container locally using Docker Commands.
 
 - Elastic Container Registry(ECR) : To push the docker image to ECR which will store the image, and we can retrieve and use the DockerImage in secure and efficient manner.
 
 - Elastic Kubernetes Service(EKS) : To create a nodes and deploy the application on kubernetes
 
 - Kubernetes : To kubectl the commands to access our pods and deployment.

## Prerequisites!

- AWS Account

- Programmatic access and AWS configured with CLI

- Python3 Installed

- Docker and Kubectl Installed

- Code editor

## Create a monitoring application using Flask

Create the Python Apllication using the "app.py" provided in the files section

```bash
import psutil 
from flask import Flask, render_template

app=Flask(__name__)

@app.route("/")
def index():
    cpu_percent = psutil.cpu_percent()
    mem_percent = psutil.virtual_memory().percent
    Message = None
    if cpu_percent > 80 or mem_percent>80:
        Message = "High CPU or Memory utilization. Please scale up!!"
    return render_template("index.html",cpu_metric=cpu_percent, mem_metric=mem_percent, message=Message)
if __name__ == "__main__" :
    app.run(debug=True,host="0.0.0.0")
```

In above code the Flask App is designed in such a way that it'll keep track of your CPU and MEMORY utilsation continuously and it'll alert you with specified Message whenever the CPU or MEMORY utilisation exceeds the 80%.
I have used the psutil module of Python which contains the build in functions like "cpu_percent()" and "virtual_memory().percent" which will show us the current CPU and MEMORY percent accordingly.

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

The steps can be visualised using the below image.

![image](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/9b8cdea1-d4c6-4e81-b874-7a0272d568ef)


Step 1. To Dockerised the Application you need to create the DockerFile. The DockerFile is provided in files section as well or you can refer it from below:

```bash
FROM python:3.9-slim-buster

WORKDIR /app

COPY requirements.txt .

RUN pip3 install --no-cache-dir -r requirements.txt

COPY . .

ENV FLASK_RUN_HOST=0.0.0.0

EXPOSE 5000

CMD ["flask","run"]
```

The Python base image used in the above file is "python:3.9-slim-buster".



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

```bash
import boto3

ecr_client = boto3.client('ecr')

repository_name = "my-cloud-native-repo"
response  = ecr_client.create_repository(repositoryName = repository_name)

repository_uri = response['repository']['repositoryUri']
print(repository_uri)
```

Run the above code using command:
```bash
python3 ecr.py
```

Once the ecr.py file is run successfully using the above command you'll get your Repository Uri which is been created in your AWS ECR.

Step 2. Push the DockerImage to ECR

Run the command's which are used to push the image. You can view those command in "View push command" section.Kindly refer the push command from your ECR repository.

![push-commands](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/329fa291-478f-4fa3-90ac-92fa6189690d)

Step 3. Once you run all the commands in proper order the DockerImage will be pushed into your ECR and you can view it under the "image" tab

![Latest Image](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/a643ed04-cf71-4a4c-88b8-9e56a395cd84)

Hence uptil now the DockerImage is successfully pushed into you ECR.


## Creating the EKS cluster and Node Groups

You need to create a EKS Cluster and Nodes group in order to deploy your container in form of Kubernetes container.

Step 1. Create a EKS Cluster of your own and make sure to create a new Security Group with Port 5000 enabled.

![Security-group](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/551bcb31-2113-49f7-8b51-8c9148fb0d66)

Make sure to select the security group you created in step 2 while configuring your EKS Cluster.

![select-sg](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/c3c45bb1-a5e7-481d-80af-4b872208aa56)

Step 2. Create a Node Group with 2 nodes having Instance of tyoe "t2.micro".

## Creating the Deployment and Service for kubernetes deployment.

Step 1. Create the Deployment file

```bash
deployment = client.V1Deployment(
    metadata=client.V1ObjectMeta(name="my-flask-app"),
    spec=client.V1DeploymentSpec(
        replicas=1,
        selector=client.V1LabelSelector(
            match_labels={"app": "my-flask-app"}
        ),
        template=client.V1PodTemplateSpec(
            metadata=client.V1ObjectMeta(
                labels={"app": "my-flask-app"}
            ),
            spec=client.V1PodSpec(
                containers=[
                    client.V1Container(
                        name="my-flask-container",
                        image="{imageUri}",
                        ports=[client.V1ContainerPort(container_port=5000)]
                    )
                ],
                image_pull_secrets=[
                    client.V1LocalObjectReference(name="regcred")
                ]
            )
        )
    )
)

# Create the deployment
api_instance = client.AppsV1Api(api_client)
api_instance.create_namespaced_deployment(
    namespace="default",
    body=deployment
)
```

Step 2. Create The Service 
```bash
service = client.V1Service(
    metadata=client.V1ObjectMeta(name="my-flask-service"),
    spec=client.V1ServiceSpec(
        selector={"app": "my-flask-app"},
        ports=[client.V1ServicePort(port=5000)]
    )
)

# Create the service
api_instance = client.CoreV1Api(api_client)
api_instance.create_namespaced_service(
    namespace="default",
    body=service
)
```

Run the above "eks.py" file containing the deployment and service in default namespace using command :
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

``bash
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

![image](https://github.com/adilshaikh165/devops-monitoring-cloudApp/assets/98637502/8d2a67dc-f6c2-4e43-b8c6-f1af148e7c13)


























