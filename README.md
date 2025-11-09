#  Student Registration Website.



##  Frontend -
Node.js and npm must be installed.

##  Backend -
Java Development Kit (JDK 17 or higher) must be installed.

Maven must be installed.

##  Database -
Install MariaDB.

##  Before starting, make sure:-

1. RDS (Relational Database Service) is created.

2. EC2 Instance is launched.
 
3. Docker install using official documention.
 
4. MySQL Client is installed.

5. Docker-compose.yml is installed.
   


##  Steps and Commands:


## 1. RDS (Relational Database Service) -

. Create Database

. Standard create 

. MariaDB

. Username - (admin) - Ex.

. Password - (redhat123) - Ex.

. Create Database

## 2. EC2 (Elastic Compute Cloud) -

. Launch instance 

. Connect


### 1. Docker install using official documention.

Set up Docker's apt repository.
```shell

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

```

To install the latest version, run:
```sh

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```

### 2. Switch to root user

```sh
sudo -i
```

### 3. Update the instance

```sh
apt update
```

### 4. Clone the GitHub Repository
```sh
git clone <GitHub_Repository_Link>
```
##### Example: git clone https://github.com/username/student-registration.git

### 5. Backend

```sh
cd <GitHub_Repository_Name>/backend

```
```sh
cp src/main/resources/application.properties .
```
#### Write application.properties =
```sh
nano application.properties
    server.port=8080
    spring.datasource.url=jdbc:mariadb://(database endpoint paste):3306/student_db 
    spring.datasource.username=(database username)
    spring.datasource.password=(database password)
    spring.jpa.hibernate.ddl-auto=update
    spring.jpa.show-sql=true
# Then ctrl s+x
```
#### Write Backend Dockerfile =
```sh
nano dockerfile
    FROM maven:3.8.3-openjdk-17
    COPY . /opt
    WORKDIR /opt
    RUN rm -rf src/main/resources/application.properties
    RUN cp -rf application.properties src/main/resources
    RUN mvn clean package
    WORKDIR /opt/target
    EXPOSE 8080
    CMD ["java","-jar","student-registration-backend-0.0.1-SNAPSHOT.jar"]
```

### 6. Frontend

```sh
cd ../frontend/
```
```sh
ls
```
#### Write .env file =
```sh
nano .env
```

##### Example: (Paste EC2 public IP)

#### Write Frontend Dockerfile =
```sh
nano dockerfile
    FROM node:25-alpine3.21
    COPY . /opt
    WORKDIR /opt
    RUN apk update 
    RUN apk add apache2
    RUN npm install
    RUN npm run build
    RUN cp -rf dist/* /var/www/localhost/htdocs/
    EXPOSE 80
    CMD ["httpd","-D","FOREGROUND"]
```

### 7. Mysql-client install
```sh
apt install mysql-client -y
```
```sh
mysql -h (endpoint) -u (username) -p
```
```sh
Enter password (password)
```
##### RDS Database Endpoint copy & paste
##### Example: mysql -h database-1.ca9eie2mihs7.us-east-1.rds.amazonaws.com -u admin -p
##### Example: redhat123

```sh
CREATE DATABASE student_db;
```
```sh
GRANT ALL PRIVILEGES ON springbackend.* TO 'username'@'localhost' IDENTIFIED BY 'password';
```
```sh
USE student_db;
```
```sh
CREATE TABLE `students` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  `course` varchar(255) DEFAULT NULL,
  `student_class` varchar(255) DEFAULT NULL,
  `percentage` double DEFAULT NULL,
  `branch` varchar(255) DEFAULT NULL,
  `mobile_number` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=80 DEFAULT CHARSET=latin1 COLLATE=latin1_swedish_ci;
```
```sh
show databases;
```
```sh
exit;
```

### 8. Docker-compose.yml 

```sh
cd ..
```
```sh
nano docker-compose.yml

version: "3.8"
services: 
  backend:
    build:
      context: ./backend
      dockerfile: dockerfile
    ports:
      - "8080:8080"
 

  frontend:
    build: 
      context: ./frontend 
      dockerfile: dockerfile
    ports:
      - "80:80" 
    depends_on:
       - backend


```
