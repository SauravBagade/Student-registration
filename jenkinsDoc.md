# 📘 **Student Registration App — AWS CI/CD Deployment Guide**

This documentation explains how to deploy the `Student-registration` application using:

✔ **AWS EC2** (Jenkins + Backend + Frontend)
✔ **AWS RDS** (MariaDB Database)
✔ **Nginx/Apache** (Frontend Hosting)
✔ **Jenkins Pipeline CI/CD**

---

## 🟦 **1. Architecture Overview**

**Components:**

* **Frontend:** React + Vite
* **Backend:** Spring Boot (MariaDB)
* **Database:** AWS RDS (MariaDB)
* **CI/CD:** Jenkins
* **Hosting:** Apache2 (Static Files)
* **Infrastructure:** AWS EC2 + RDS

**Flow:**

Frontend → Backend (EC2) → Database (RDS)

---

## 🟦 **2. AWS RDS Setup (MariaDB)**

1. Open AWS Console → `RDS → Create Database`
2. Select:

   * **Engine:** MariaDB
3. Configure DB values:

| Parameter     | Value          |
| ------------- | -------------- |
| Instance Type | `db.t2.medium` |
| DB Name       | `student_db`   |
| Username      | `root`         |
| Password      | `Redhat123`    |
| Storage       | As required    |

4. Create DB and note the **RDS Endpoint**

---

## 🟦 **3. AWS EC2 Setup (Jenkins Server)**

1. Launch an EC2 Instance:

| Parameter     | Value       |
| ------------- | ----------- |
| Instance Type | `t2.medium` |
| OS            | Ubuntu      |
| Key Pair      | Any         |

2. Add **User Data Script:**

```bash
#!/bin/bash
apt-get update -y
apt-get upgrade -y

apt install -y openjdk-17-jdk

mkdir -p /etc/apt/keyrings
wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" \
| tee /etc/apt/sources.list.d/jenkins.list > /dev/null

apt-get update -y
apt-get install -y jenkins

systemctl start jenkins
systemctl enable jenkins
```

3. Open Jenkins in browser:

```
http://<EC2-IP>:8080
```

---

## 🟦 **4. Configure Jenkins Permissions**

SSH into EC2:

```bash
ssh -i your-key.pem ubuntu@<EC2-IP>
sudo -i
```

Edit sudoers:

```bash
visudo
```

Add:

```
jenkins ALL=(ALL) NOPASSWD:ALL
```

---

## 🟦 **5. Application Configuration**

### **Backend Config**

Edit:
'cd /var/lib/jenkins/workspace/my-job'

`Student-registration/backend/src/main/resources/application.properties`

```properties
server.port=8081
spring.datasource.url=jdbc:mariadb://<RDS-ENDPOINT>:3306/student_db
spring.datasource.username=root
spring.datasource.password=Redhat123
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MariaDBDialect
```

### **Frontend Config**

Edit:
'cd /var/lib/jenkins/workspace/my-job'
`Student-registration/frontend/.env`

```env
VITE_API_URL="http://<EC2-IP>:8081/api"
```

---

## 🟦 **6. Jenkins Pipeline Configuration**

Create **Pipeline Job** and use this script:

```groovy
pipeline {
    agent any

    stages {

        stage('Install Packages') {
            steps {
                sh '''
                  sudo apt update -y
                  sudo apt install -y openjdk-17-jdk maven nodejs npm apache2
                  java -version
                  mvn -version
                  node -v
                  npm -v
                '''
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/SauravBagade/DevOps-Projects.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('Student-registration/backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Run Backend') {
            steps {
                dir('Student-registration/backend/target') {
                    sh 'pkill -f "java -jar" || true'
                    sh 'nohup java -jar *.jar > app.log 2>&1 &'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('Student-registration/frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Deploy to Apache') {
            steps {
                sh '''
                    sudo systemctl start apache2
                    sudo rm -rf /var/www/html/*
                    sudo cp -rf Student-registration/frontend/dist/* /var/www/html/
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deployment Completed Successfully!'
            }
        }
    }
}
```

---

## 🟦 **7. Verification Steps**

### **Backend Check**

SSH into EC2:

```bash
ps aux | grep java
```

OR:

```bash
curl http://localhost:8081/api
```

### **Frontend Check**

Open in browser:

```
http://<EC2-IP>
```

