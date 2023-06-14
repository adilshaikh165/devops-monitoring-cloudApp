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
pip3 install requirements.txt
```
The app will be runned on localhost

