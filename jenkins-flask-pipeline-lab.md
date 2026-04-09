# Jenkins CI/CD Pipeline using Docker and GitHub

## 🎯 Objective

To demonstrate a complete CI/CD pipeline using:

* Jenkins (running in Docker)
* Docker (for containerization)
* GitHub (for source code management)
* Flask (simple web application)

---

# 🔷 Part 1: Jenkins Setup in Docker

## Step 1: Run Jenkins Container

```bash
docker run -d -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock --name jenkins-container jenkins/jenkins:lts
```

## Step 2: Access Jenkins

Open browser:

```
http://localhost:8080
```

## Step 3: Get Initial Password

```bash
docker exec jenkins-container cat /var/jenkins_home/secrets/initialAdminPassword
```

## Step 4: Install Plugins

* Choose: **Install Suggested Plugins**

---

# 🔷 Part 2: Install Docker in Jenkins Container

## Step 1: Enter Jenkins Container

```bash
docker exec -u root -it jenkins-container bash
```

## Step 2: Install Docker CLI

```bash
apt update
apt install -y docker.io
```

## Step 3: Verify Docker

```bash
docker --version
docker ps
```

---

# 🔷 Part 3: Fix Docker Permission Issue

```bash
chmod 666 /var/run/docker.sock
```

## Verify Access

```bash
docker exec -it jenkins-container docker ps
```

---

# 🔷 Part 4: Create GitHub Project

## Project Structure

```
jenkins-ci-cd-demo/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── Jenkinsfile
└── README.md
```

---

# 🔷 Part 5: Application Code

## app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello Students! CI/CD Pipeline is Working 🚀"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

---

## requirements.txt

```
flask
```

---

# 🔷 Part 6: Docker Configuration

## Dockerfile

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

# 🔷 Part 7: Jenkins Pipeline

## Jenkinsfile

```groovy
pipeline {
    agent any

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-demo-app .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker rm -f flask-container || true'
                sh 'docker run -d -p 5000:5000 --name flask-container flask-demo-app'
            }
        }
    }
}
```

---

# 🔷 Part 8: Jenkins Job Configuration

1. Open Jenkins Dashboard
2. Click **New Item**
3. Enter name: `jenkins-pipeline`
4. Select **Pipeline**
5. Click OK

## Configure:

* Definition: Pipeline script from SCM
* SCM: Git
* Repository URL: Your GitHub repo URL
* Branch: */main
* Script Path: Jenkinsfile

---

# 🔷 Part 9: Run Pipeline

Click:

```
Build Now
```

---

# 🔷 Part 10: Output

Open browser:

```
http://localhost:5000
```

## Expected Output

```
Hello Students! CI/CD Pipeline is Working 🚀
```

---

# 🎓 Key Learning Outcomes

* Understand CI/CD pipeline
* Integrate GitHub with Jenkins
* Build Docker images automatically
* Deploy applications using containers

---

# 🔥 Common Errors & Fixes

| Error             | Cause                | Fix                             |
| ----------------- | -------------------- | ------------------------------- |
| docker not found  | Docker not installed | Install docker inside container |
| permission denied | Socket issue         | chmod 666 /var/run/docker.sock  |
| branch not found  | master vs main       | Use main branch                 |

---

# 🚀 Conclusion

This experiment demonstrates a complete CI/CD workflow where:

* Code is stored in GitHub
* Jenkins automates build and deployment
* Docker ensures consistent execution environment

---

**End of Lab Manual**
