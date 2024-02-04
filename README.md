## CLO835_Assignment1
___

##Running Terraform
___
```bash
ssh-keygen -t rsa -f clo835
terraform init
terraform validate
terraform plan
terraform apply
```

##Logging into ec2-instance
___
```bash
ssh -i 'key' ec2-user@<instance-public-ip>
```

##Configuring credentials in the ec2-instance
___
```bash
export AWS_ACCESS_KEY_ID=ASIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
export AWS_SESSION_TOKEN=AQoDYXdzEJr...<remainder of session token>
```

##Installing docker on the ec2-instance
___
```bash
sudo yum install docker
```

##Logging into repository
___
```bash
aws ecr get-login-password --region us-east-1 | sudo docker login -u AWS  --password-stdin 945233852280.dkr.ecr.us-east-1.amazonaws.com/mysqlrepo
aws ecr get-login-password --region us-east-1 | sudo docker login -u AWS  --password-stdin 945233852280.dkr.ecr.us-east-1.amazonaws.com/apprepo
```

##Configuring permisiions
___
```bash
sudo chmod 666 /var/run/docker.sock
```

##Creating custom bridge network
___
```bash
docker create network net
```

##Running mysql container
___
```bash
sudo docker run  --network net -d -e  MYSQL_ROOT_PASSWORD=pw 945233852280.dkr.ecr.us-east-1.amazonaws.com/mysqlrepo:v0.1
```
##Environment variables for the application
___
```bash
export DBHOST=172.17.0.2
export DBPORT=3306
export DBUSER=root
export DATABASE=employees
export DBPWD=pw
```

##Running application container
___
```bash
docker run --name blue --network net -d -p 8081:8080 -e APP_COLOR=blue -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e  DBUSER=$DBUSER -e DBPWD=$DBPWD 945233852280.dkr.ecr.us-east-1.amazonaws.com/apprepo:v0.1
docker run --name pink --network net -d -p 8082:8080 -e APP_COLOR=pink -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e  DBUSER=$DBUSER -e DBPWD=$DBPWD 945233852280.dkr.ecr.us-east-1.amazonaws.com/apprepo:v0.1
docker run --name lime --network net -d -p 8083:8080 -e APP_COLOR=lime -e DBHOST=$DBHOST -e DBPORT=$DBPORT -e  DBUSER=$DBUSER -e DBPWD=$DBPWD 945233852280.dkr.ecr.us-east-1.amazonaws.com/apprepo:v0.1
```

##Logging into container
___
```bash
docker exec -it pink sh
```

#Installing iputils-ping package inside the container
___
```bash
apt-get update -y
apt-get iputils-ping
```

##Pinging other containers
___
```bash
ping blue
ping lime
```
