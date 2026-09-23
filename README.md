# 🚀 AWS Python Web Application

A practical cloud deployment project demonstrating how to build, containerize, store, and deploy a Python Flask web application using **AWS, Docker, Amazon ECR, EC2, Lambda, and ECS/Fargate**.

---

## 🏗️ Architecture

```text
                         Internet
                            |
                            v
                    Application Access
                            |
             +--------------+--------------+
             |                             |
             v                             v
          EC2 + Docker                API Gateway
             |                             |
             v                             v
        Amazon ECR                     AWS Lambda
             |
             v
       Python Flask App
             
                    Planned ECS Deployment
                            |
                            v
                       ECS Fargate
                            |
                            v
                       Docker Task
                            |
                            v
                       Amazon ECR
```

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Python | Application development |
| Flask | Web framework |
| Docker | Application containerization |
| Amazon ECR | Docker image registry |
| Amazon EC2 | Docker application server |
| AWS Lambda | Serverless Python function |
| Amazon ECS/Fargate | Container orchestration |
| API Gateway | Lambda API endpoint |
| IAM | AWS permissions |
| CloudWatch | Logs and monitoring |
| Git/GitHub | Source code management |

The project documentation identifies EC2, Docker, ECR, ECS, Lambda, IAM, and CloudWatch as the primary services practiced in the project.

---

# 📁 Project Structure

```text
aws-python-webapp/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── README.md
│
└── templates/
    └── index.html
```

---

# 🐍 Flask Application

The Flask application provides three endpoints:

### Home

```text
GET /
```

Displays the web application.

### Hello API

```text
GET /api/hello
```

Example response:

```json
{
  "message": "Hello from Python Flask!",
  "status": "success"
}
```

### Application Information

```text
GET /api/info
```

Example response:

```json
{
  "application": "AWS Python Web App",
  "platform": "Docker",
  "deployment": "ECS"
}
```

These routes are based on the Flask application defined in the project document.

---

# 💻 Run Locally on Ubuntu

## 1. Clone the repository

```bash
git clone https://github.com/YOUR-USERNAME/aws-python-webapp.git
```

```bash
cd aws-python-webapp
```

## 2. Create virtual environment

```bash
python3 -m venv venv
```

Activate it:

```bash
source venv/bin/activate
```

## 3. Install dependencies

```bash
pip install -r requirements.txt
```

## 4. Run Flask

```bash
python app.py
```

Open:

```text
http://localhost:5000
```

---

# 🐳 Docker Deployment

## Build Docker image

```bash
docker build -t aws-python-webapp .
```

## Check image

```bash
docker images
```

## Run container

```bash
docker run -d \
  -p 5000:5000 \
  --name python-webapp \
  aws-python-webapp
```

Open:

```text
http://localhost:5000
```

The Docker configuration uses Python 3.12, exposes port 5000, and starts the Flask application with `python app.py`.

---

# ☁️ Amazon ECR

## ECR Repository

```text
Repository:
aws-python-webapp
```

AWS Region:

```text
ap-south-1
```

ECR repository URI:

```text
192905952686.dkr.ecr.ap-south-1.amazonaws.com/aws-python-webapp
```

> For a public GitHub repository, consider replacing the account-specific URI above with a placeholder.

---

## Login to ECR

```bash
aws ecr get-login-password \
  --region ap-south-1 | \
docker login \
  --username AWS \
  --password-stdin \
  192905952686.dkr.ecr.ap-south-1.amazonaws.com
```

Expected:

```text
Login Succeeded
```

## Tag image

```bash
docker tag aws-python-webapp:latest \
192905952686.dkr.ecr.ap-south-1.amazonaws.com/aws-python-webapp:latest
```

## Push image

```bash
docker push \
192905952686.dkr.ecr.ap-south-1.amazonaws.com/aws-python-webapp:latest
```

The project workflow uses ECR to store the Docker image before deployment.

---

# 🖥️ EC2 Deployment

An Ubuntu EC2 instance is used as a Docker host.

## Install Docker

```bash
sudo apt update
sudo apt install docker.io -y
```

Enable Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Check:

```bash
docker --version
```

---

## Login to ECR from EC2

```bash
aws ecr get-login-password \
--region ap-south-1 | \
sudo docker login \
--username AWS \
--password-stdin \
192905952686.dkr.ecr.ap-south-1.amazonaws.com
```

## Pull Docker image

```bash
sudo docker pull \
192905952686.dkr.ecr.ap-south-1.amazonaws.com/aws-python-webapp:latest
```

## Run application

```bash
sudo docker run -d \
-p 5000:5000 \
--name python-webapp \
192905952686.dkr.ecr.ap-south-1.amazonaws.com/aws-python-webapp:latest
```

Check:

```bash
sudo docker ps
```

---

# 🌐 Access Application

Allow TCP port **5000** in the EC2 Security Group.

Then open:

```text
http://EC2-PUBLIC-IP:5000
```

Example:

```text
http://YOUR-EC2-PUBLIC-IP:5000
```

The project documentation specifies accessing the EC2-hosted application through port 5000.

---

# ⚡ AWS Lambda

The project also includes a serverless Python Lambda function.

Function:

```text
PythonWebAppFunction
```

Runtime:

```text
Python 3.12
```

Handler:

```text
lambda_function.lambda_handler
```

Example response:

```json
{
  "message": "Hello from AWS Lambda!",
  "application": "Python Web App",
  "status": "success"
}
```

The Lambda portion of the project uses Python 3.12 and returns this JSON response.

---

# 🔌 API Gateway + Lambda

Planned serverless request flow:

```text
Client
  |
  v
API Gateway
  |
  v
AWS Lambda
  |
  v
Python Function
  |
  v
CloudWatch
```

Example API:

```text
GET /hello
```

The project document describes API Gateway HTTP API integration with Lambda.

---

# 🚢 ECS/Fargate Deployment

The containerized application can also be deployed using Amazon ECS with Fargate.

Example task configuration:

```text
Task Family:
python-webapp-task

Container:
python-webapp

Container Port:
5000

CPU:
0.5 vCPU

Memory:
1 GB
```

The Docker image is retrieved from Amazon ECR.

---

# 🏗️ Final Architecture

```text
                         USER
                           |
                           v
                 Application Load Balancer
                           |
                           v
                     ECS Service
                     /          \
                    v            v
               ECS Task 1    ECS Task 2
                    |            |
                    v            v
               Docker        Docker
               Container     Container
                    \            /
                     \          /
                          v
                         ECR
                          |
                          v
                    Docker Image
```

Separate serverless path:

```text
USER
 |
 v
API Gateway
 |
 v
Lambda
 |
 v
Python Function
 |
 v
CloudWatch
```

This reflects the final architecture described in the project document.

---

# 🔐 AWS Security

Recommended practices:

- Use IAM roles instead of storing AWS access keys in application code.
- Do not commit `.pem` files.
- Do not commit AWS access keys or secret keys.
- Restrict Security Group inbound rules where possible.
- Use HTTPS for production applications.
- Keep Docker images and dependencies updated.
- Enable monitoring and logging with CloudWatch.

---

# 🧹 Useful Docker Commands

Check running containers:

```bash
docker ps
```

Stop container:

```bash
docker stop python-webapp
```

Remove container:

```bash
docker rm python-webapp
```

List images:

```bash
docker images
```

Remove image:

```bash
docker rmi aws-python-webapp
```

View logs:

```bash
docker logs python-webapp
```

---

# 📌 Project Status

| Component | Status |
|---|---|
| Flask Application | ✅ Completed |
| Dockerfile | ✅ Completed |
| Docker Image | ✅ Completed |
| ECR Repository | ✅ Completed |
| ECR Authentication | ✅ Completed |
| Docker Image Push | ✅ Completed |
| EC2 Docker Host | ✅ Completed |
| ECR → EC2 Deployment | 🔄 In Progress |
| Lambda | 🔄 Planned |
| API Gateway | 🔄 Planned |
| ECS/Fargate | 🔄 Planned |
| Load Balancer | 🔄 Planned |
| CloudWatch | 🔄 Planned |

---

# 🎯 Learning Objectives

This project demonstrates practical experience with:

- Python Flask application development
- Linux/Ubuntu administration
- Docker containerization
- Amazon ECR
- Amazon EC2
- AWS IAM
- AWS Lambda
- API Gateway
- Amazon ECS/Fargate
- Container deployment
- CloudWatch monitoring
- Git and GitHub
- AWS cloud architecture

---

# 👨‍💻 Author

**Pakeer Mydeen**

GitHub:

```text
https://github.com/pakeermydeen
```

---

# 📜 License

This project is created for educational and portfolio purposes.