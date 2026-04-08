# 🧪 End-to-End Jenkins Pipeline with GitHub Webhook (Flask App)

## 🎯 Objective
In this lab, you will:
- Build a simple Flask application
- Write unit tests using pytest
- Push code to GitHub
- Configure Jenkins Pipeline
- Enable automatic build trigger using Webhooks

---

# 🧩 1. Application Layer (Flask App)

## app.py
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Jenkins Pipeline!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### Explanation
- Creates a simple Flask web server
- "/" is the homepage route
- Returns a simple message

---

## test_app.py
```python
from app import app

def test_home():
    client = app.test_client()
    response = client.get("/")
    assert response.status_code == 200
    assert b"Hello, Jenkins Pipeline!" in response.data
```

### Explanation
- Uses pytest to test the endpoint
- Ensures the app is working correctly

---

## requirements.txt
```
flask
pytest
```

---

## Dockerfile
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 5000
CMD ["python", "app.py"]
```

---

# 🧑‍💻 2. GitHub Setup

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/<username>/<repo>.git
git branch -M main
git push -u origin main
```

---

# 🔐 3. Jenkins Setup

## Run ngrok (if local)
```bash
ngrok http 8080
```

Use generated URL in webhook.

---

# ⚙️ 4. Jenkinsfile

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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-app .'
            }
        }
    }

    post {
        success {
            echo "Build Successful!"
        }
        failure {
            echo "Build Failed!"
        }
        always {
            echo "Pipeline Completed"
        }
    }
}
```

---

# 🔗 5. GitHub Webhook

- URL: http://<jenkins-url>/github-webhook/
- Content Type: application/json
- Event: Push

---

# 🎬 6. Demo

```bash
git add .
git commit -m "Test webhook"
git push origin main
```

Observe Jenkins auto-trigger.

---

# ⚠️ Troubleshooting

| Issue | Fix |
|------|-----|
| Webhook not working | Check GitHub logs |
| Jenkins not accessible | Use ngrok |
| Docker permission denied | Add Jenkins to docker group |
| pytest not found | Activate venv |

---

# 🚀 Outcome
- Automated CI pipeline
- GitHub → Jenkins integration
- Docker build automation
