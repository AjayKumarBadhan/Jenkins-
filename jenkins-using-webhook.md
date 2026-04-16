# 🚀 CI/CD using GitHub Webhooks with Jenkins (Complete Lab Guide)

---

# 📌 Objective

This lab demonstrates how to:

* Trigger Jenkins automatically using GitHub Webhooks
* Build and execute a Python application
* Understand CI/CD workflow practically

---

# 🧠 Concept Overview

**Webhook Flow:**

```
Developer → Git Push → GitHub → Webhook → Jenkins → Build → Output
```

---

# ⚙️ Prerequisites

* Docker Desktop installed
* Jenkins running in Docker
* GitHub repository created
* Basic Git commands knowledge

---

# 🐳 Step 1: Run Jenkins in Docker

```bash
docker run -d -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts
```

Access Jenkins:

```
http://localhost:8080
```

---

# 🐍 Step 2: Install Python in Jenkins Container

```bash
docker ps

docker exec -it <container_id> bash

apt update
apt install -y python3 python3-pip
```

Verify:

```bash
python3 --version
```

---

# 🌐 Step 3: Install ngrok

## Check if installed

```bash
ngrok version
```

## If not installed

1. Download from: [https://ngrok.com/download](https://ngrok.com/download)
2. Extract ZIP
3. Move to:

```
C:\ngrok\
```

## Add to PATH

* Open Environment Variables
* Add `C:\ngrok\` to Path

## Authenticate

```bash
ngrok config add-authtoken YOUR_TOKEN
```

---

# 🌍 Step 4: Start ngrok

```bash
ngrok http 8080
```

Example Output:

```
https://abcd1234.ngrok.io
```

---

# 🔗 Step 5: Configure GitHub Webhook

Go to:

```
GitHub → Repo → Settings → Webhooks → Add Webhook
```

Payload URL:

```
https://abcd1234.ngrok.io/github-webhook/
```

Content Type:

```
application/json
```

Event:

```
Just the push event
```

---

# 🧪 PROGRAM 1: Basic Webhook CI

## 📁 Project Structure

```
ci-webhook-demo-1/
│── app.py
│── requirements.txt
```

## 💻 Code

### app.py

```python
print("Hello, CI/CD with Webhooks!")
```

### requirements.txt

```
# Empty file
```

---

## ⚙️ Jenkins Job Setup

1. Create **Freestyle Project**
2. Configure Git repo
3. Enable trigger:

```
GitHub hook trigger for GITScm polling
```

4. Build Step:

```bash
python3 app.py
```

---

## ▶ Execution Steps

```bash
git add .
git commit -m "Webhook demo"
git push origin main
```

---

## ✅ Expected Output (Jenkins Console)

```
Started by GitHub push
Hello, CI/CD with Webhooks!
Finished: SUCCESS
```

---

# 🧪 PROGRAM 2: Pipeline CI (Jenkinsfile)

## 📁 Structure

```
ci-webhook-demo-2/
│── app.py
│── Jenkinsfile
```

## 💻 Code

### app.py

```python
print("Pipeline CI/CD execution successful")
```

### Jenkinsfile

```groovy
pipeline {
    agent any

    stages {

        stage('Clone') {
            steps {
                echo 'Cloning Repository...'
            }
        }

        stage('Build') {
            steps {
                echo 'Building Application...'
            }
        }

        stage('Run') {
            steps {
                sh 'python3 app.py'
            }
        }
    }
}
```

---

## 🧠 Code Explanation

### pipeline {}

Defines the CI/CD workflow

### agent any

Run on any available Jenkins node

### stages {}

Different steps in pipeline

### stage('Run')

Executes Python program

---

# 🧪 PROGRAM 3: CI/CD with Docker

## 📁 Structure

```
ci-webhook-demo-3/
│── app.py
│── Dockerfile
│── Jenkinsfile
```

## 💻 Code

### app.py

```python
print("Docker CI/CD Pipeline Executed Successfully!")
```

### Dockerfile

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
CMD ["python", "app.py"]
```

---

## 🧠 Dockerfile Explanation

* FROM → Base image
* WORKDIR → Working directory
* COPY → Copy files
* CMD → Command to execute

---

### Jenkinsfile

```groovy
pipeline {
    agent any

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ci-demo-app .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run --rm ci-demo-app'
            }
        }
    }
}
```

---

# 🎯 Expected Output

```
Docker CI/CD Pipeline Executed Successfully!
```

---

# 🔍 Troubleshooting

## Issue: python not found

✔ Install python3 in container

## Issue: webhook not triggering

✔ Check ngrok running
✔ Verify webhook URL

## Issue: build fails

✔ Check console output logs

---

# 🎓 Viva Questions

1. What is a webhook?
2. Difference between CI and CD?
3. Why use Jenkinsfile?
4. Why ngrok is required?
5. Difference between polling and webhook?

---

# 🧠 Key Learning

* Webhooks automate CI
* Jenkins executes pipeline
* Docker enables deployment

---

# ✅ Conclusion

You have successfully implemented:

* Webhook-based CI/CD
* Jenkins automation
* Docker-based deployment

---

**End of Lab Guide** 🎉
