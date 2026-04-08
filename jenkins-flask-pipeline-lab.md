# 🧪 COMPLETE BEGINNER LAB: Jenkins + GitHub + Flask + Docker (Step-by-Step)

## 🎯 Objective
This lab is designed for absolute beginners. By the end, you will:
- Build a simple Python Flask web app
- Write test cases using pytest
- Push code to GitHub
- Run Jenkins inside Docker Desktop
- Connect GitHub with Jenkins using Webhooks
- Automate builds on every git push

---

# 🧩 PART 1: What is Each Tool? (Basic Understanding)

## 🔹 Flask
A lightweight Python framework used to build web applications.

## 🔹 Git & GitHub
- Git → Version control tool
- GitHub → Cloud platform to store code

## 🔹 Jenkins
Automation tool used for CI/CD (Continuous Integration)

## 🔹 Docker Desktop
Runs containers (lightweight virtual environments)

## 🔹 ngrok
Tool to expose your local Jenkins to the internet

---

# 🧩 PART 2: Create Flask Application

## 📄 Step 1: app.py
```python
from flask import Flask

# Create Flask app
app = Flask(__name__)

# Define route (homepage)
@app.route("/")
def home():
    return "Hello, Jenkins Pipeline!"

# Run server
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### 🧠 Explanation
- Flask() → creates app
- @app.route("/") → homepage URL
- function returns text to browser
- host 0.0.0.0 → accessible from outside container

---

## 📄 Step 2: test_app.py
```python
from app import app

def test_home():
    client = app.test_client()
    response = client.get("/")
    assert response.status_code == 200
    assert b"Hello, Jenkins Pipeline!" in response.data
```

### 🧠 Explanation
- test_client() → simulates browser
- assert → checks correctness

---

## 📄 Step 3: requirements.txt
```
flask
pytest
```

---

## 📄 Step 4: Dockerfile
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
EXPOSE 5000
CMD ["python", "app.py"]
```

---

# 🧩 PART 3: GitHub Setup

## 🔧 Commands
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/<username>/<repo>.git
git branch -M main
git push -u origin main
```

---

# 🧩 PART 4: Run Jenkins using Docker Desktop

## Step 1: Pull Jenkins Image
```bash
docker pull jenkins/jenkins:lts
```

## Step 2: Run Jenkins Container
```bash
docker run -d -p 8080:8080 -p 50000:50000 --name jenkins jenkins/jenkins:lts
```

## Step 3: Get Initial Password
```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

## Step 4: Open Jenkins
Open browser:
http://localhost:8080

---

# 🧩 PART 5: Install ngrok (From Scratch)

## Step 1: Download ngrok
Go to official website:
https://ngrok.com/download

## Step 2: Extract ZIP file

## Step 3: Run ngrok
```bash
ngrok http 8080
```

## Step 4: Copy URL
Example:
https://abc123.ngrok.io

---

# 🧩 PART 6: Jenkins Pipeline

## 📄 Jenkinsfile
```groovy
pipeline {
    agent any

    triggers {
        githubPush()
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/<username>/<repo>.git'
            }
        }

        stage('Install') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                . venv/bin/activate
                pytest
                '''
            }
        }

        stage('Build Docker') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }
    }

    post {
        success { echo "SUCCESS" }
        failure { echo "FAILED" }
        always { echo "DONE" }
    }
}
```

---

# 🧩 PART 7: GitHub Webhook

Go to:
Repo → Settings → Webhooks → Add

Payload URL:
http://<ngrok-url>/github-webhook/

Content Type:
application/json

---

# 🧩 PART 8: Jenkins Configuration

- Create Pipeline Job
- Select "Pipeline script from SCM"
- Enable:
GitHub hook trigger for GITScm polling

---

# 🧩 PART 9: Demo

```bash
git add .
git commit -m "trigger test"
git push origin main
```

Observe Jenkins auto build.

---

# ⚠️ Troubleshooting

| Problem | Solution |
|--------|--------|
| Jenkins not accessible | Check Docker running |
| ngrok not working | Restart ngrok |
| Webhook failed | Check URL |
| pytest error | install dependencies |

---

# 🚀 Final Output

You have successfully built:
- Flask App
- Docker Image
- Jenkins Pipeline
- GitHub Integration
- Auto Trigger CI/CD
