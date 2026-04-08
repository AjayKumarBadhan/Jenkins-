# 🧪 Complete Beginner Lab: Jenkins + GitHub + Flask (From Scratch)

## 🎯 Objective
This lab is designed for absolute beginners. By the end, you will:
- Build a simple Python Flask application
- Write test cases using pytest
- Push code to GitHub
- Install and configure Jenkins (Docker assumed)
- Install and use ngrok
- Configure Webhooks for automatic triggering
- Build a CI pipeline using Jenkins

---

# 🧩 PART 1: What is Flask?

Flask is a lightweight Python web framework used to build web applications.

---

## Step 1: Create app.py

```python
from flask import Flask

# Create application object
app = Flask(__name__)

# Define route (homepage)
@app.route("/")
def home():
    return "Hello, Jenkins Pipeline!"

# Run server
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### Explanation:
- Flask → library to create web apps
- app = Flask(__name__) → initializes app
- @app.route("/") → URL endpoint
- return → what user sees in browser
- app.run() → starts server

---

## Step 2: Install Python & Run App

```bash
python app.py
```

Open browser:
```
http://localhost:5000
```

---

# 🧪 PART 2: Testing using pytest

## Create test_app.py

```python
from app import app

def test_home():
    client = app.test_client()
    response = client.get("/")
    assert response.status_code == 200
```

### Explanation:
- test_client() simulates browser
- assert checks correctness

---

## Install pytest

```bash
pip install pytest
pytest
```

---

# 📦 PART 3: requirements.txt

```
flask
pytest
```

Used to install dependencies automatically.

---

# 🐳 PART 4: Dockerfile

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

---

# 🧑‍💻 PART 5: GitHub Setup

```bash
git init
git add .
git commit -m "first commit"
git remote add origin https://github.com/<username>/<repo>.git
git push -u origin main
```

---

# 🔐 PART 6: Install ngrok (IMPORTANT)

ngrok exposes your local server to internet.

## Windows Steps:
1. Go to https://ngrok.com/download
2. Download zip
3. Extract it
4. Open Command Prompt in that folder

## Linux:
```bash
sudo apt update
sudo apt install unzip
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.zip
unzip ngrok*.zip
sudo mv ngrok /usr/local/bin
```

## Authenticate:
- Signup on ngrok website
- Copy auth token

```bash
ngrok config add-authtoken <your-token>
```

## Run:
```bash
ngrok http 8080
```

---

# ⚙️ PART 7: Jenkinsfile

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

        stage('Build') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }
    }

    post {
        success {
            echo "SUCCESS"
        }
        failure {
            echo "FAILURE"
        }
    }
}
```

---

# 🔗 PART 8: Webhook Setup

GitHub → Settings → Webhooks → Add Webhook

Payload URL:
```
http://<ngrok-url>/github-webhook/
```

Content type:
```
application/json
```

---

# 🎬 PART 9: Demo

```bash
git add .
git commit -m "test trigger"
git push
```

Jenkins will automatically start.

---

# ⚠️ Troubleshooting

| Issue | Solution |
|------|---------|
| ngrok not working | Reinstall & authenticate |
| webhook red | check URL |
| Jenkins not triggering | enable trigger |
| pytest missing | install dependencies |

---

# 🚀 Final Outcome

- Auto CI pipeline
- GitHub → Jenkins integration
- Docker build
