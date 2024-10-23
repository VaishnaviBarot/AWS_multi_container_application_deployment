# Deploying Multi-Container Application using Terraform, Docker, and GitHub Actions on AWS EC2

This project demonstrates the deployment of a multi-container application using Terraform, AWS EC2, Docker, and GitHub Actions. It includes the configuration of a MySQL database and multiple application instances running in isolated Docker containers, communicating within a custom Docker bridge network. Additionally, GitHub Actions is used to automate building and pushing Docker images to Amazon Elastic Container Registry (ECR).

## Key Features:
1. **Infrastructure Provisioning with Terraform**:
    - Generate SSH keys.
    - Use Terraform to initialize, validate, and deploy an EC2 instance.

2. **EC2 Instance Configuration**:
    - SSH into the EC2 instance using the generated key.
    - Set up AWS credentials and install Docker.

3. **Docker Setup**:
    - Create a custom Docker bridge network for container communication.
    - Run a MySQL container and multiple application containers with different environment configurations.

4. **GitHub Actions Workflow for CI/CD**:
    - Automates the build and push process for Docker images to Amazon ECR on every push to the `main` branch.
    - **Builds and pushes two Docker images**:
        - The main application image.
        - The MySQL database image.

## GitHub Actions Workflow:
- **Triggered on Push**:
    - The workflow runs on every push to the `main` branch.
- **Job: Build and Push Images**:
    - **Login to Amazon ECR**: Uses AWS credentials from GitHub secrets.
    - **Install Dependencies**: Ensures the correct dependencies are installed for building the Docker images.
    - **Build and Push Application and MySQL Images**: Builds the Docker images for both the main application and MySQL, then pushes them to ECR.

## Application Environment:
- **Database**: MySQL
- **Application**: Multiple instances of a custom application with color-specific environments (blue, pink, lime).

## Commands Used:
- Terraform for infrastructure provisioning.
- Docker for building and running containers.
- AWS CLI for ECR login.
- GitHub Actions for continuous integration and deployment.

## Usage:
Follow the provided Terraform and Docker scripts, along with the GitHub Actions workflow, to provision infrastructure, set up containers, and automate image deployment to ECR.

___

Running Terraform
___
```bash
#Generating ssh keys
ssh-keygen -t rsa -f clo835
#Running Terraform code
terraform init
terraform validate
terraform plan
terraform apply


```

Logging into ec2-instance
___
```bash
ssh -i 'key' ec2-user@<instance-public-ip>
```

Configuring credentials in the ec2-instance
___
```bash
export AWS_ACCESS_KEY_ID=ASIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
export AWS_SESSION_TOKEN=AQoDYXdzEJr...<remainder of session token>
```

Installing docker on the ec2-instance
___
```bash
sudo yum install docker
```

Logging into repository
___
```bash
aws ecr get-login-password --region us-east-1 | sudo docker login -u AWS  --password-stdin 945233852280.dkr.ecr.us-east-1.amazonaws.com/mysqlrepo
```

Configuring permissions
___
```bash
sudo usermod -a -G docker ec2-user
```

Creating custom bridge network
___
```bash
docker network create net --subnet 111.111.0.0/24 --driver bridge
```

Running mysql container
___
```bash
sudo docker run  --network net -d -e  MYSQL_ROOT_PASSWORD=pw 945233852280.dkr.ecr.us-east-1.amazonaws.com/mysqlrepo:v0.1
```
Environment variables for the application
___
```bash
export DBHOST=111.111.0.2
export DBPORT=3306
export DBUSER=root
export DATABASE=employees
export DBPWD=pw
```

Running application container
___
```bash
docker run --name blue --network net -d -p 8081:8080 -e APP_COLOR=blue -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e  DBUSER=$DBUSER -e DBPWD=$DBPWD 945233852280.dkr.ecr.us-east-1.amazonaws.com/apprepo:v0.1
docker run --name pink --network net -d -p 8082:8080 -e APP_COLOR=pink -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e  DBUSER=$DBUSER -e DBPWD=$DBPWD 945233852280.dkr.ecr.us-east-1.amazonaws.com/apprepo:v0.1
docker run --name lime --network net -d -p 8083:8080 -e APP_COLOR=lime -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e  DBUSER=$DBUSER -e DBPWD=$DBPWD 945233852280.dkr.ecr.us-east-1.amazonaws.com/apprepo:v0.1
```

Logging into container
___
```bash
docker exec -it pink sh
```

Installing iputils-ping package inside the container
___
```bash
apt-get update -y
apt install iputils-ping
```

Pinging other containers
___
```bash
ping blue
ping lime
```
