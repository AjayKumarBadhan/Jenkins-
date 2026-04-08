🧪 End-to-End Jenkins Pipeline with GitHub Webhook (Flask App)
🎯 Objective

In this lab, you will:

Build a simple Flask application
Write unit tests using pytest
Push code to GitHub
Configure Jenkins Pipeline
Enable automatic build trigger using Webhooks
🧩 1. Application Layer (Flask App)
📄 Step 1: Create app.py
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Jenkins Pipeline!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

🧠 Explanation (From Scratch)
from flask import Flask → Imports Flask framework
app = Flask(__name__) → Creates Flask application instance
@app.route("/") → Defines URL endpoint (homepage)
def home() → Function executed when homepage is accessed
return "Hello..." → Response sent to browser
app.run(...) → Starts the server on port 5000
🧪 Step 2: Create test_app.py
from app import app

def test_home():
    client = app.test_client()
    response = client.get("/")
    assert response.status_code == 200
    assert b"Hello, Jenkins Pipeline!" in response.data

🧠 Explanation
test_client() → Simulates browser requests
client.get("/") → Calls homepage
assert status_code == 200 → Checks success response
b"Hello..." → Byte string comparison

👉 This ensures your app works before deployment.

📦 Step 3: Create requirements.txt
flask
pytest

🧠 Explanation
Lists dependencies required by the project
Jenkins and Docker will use this file to install packages
🐳 Step 4: Create Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY . .

RUN pip install --no-cache-dir -r requirements.txt

EXPOSE 5000

CMD ["python", "app.py"]

🧠 Explanation
FROM → Base image
WORKDIR → Sets working directory
COPY . . → Copies project files
RUN pip install → Installs dependencies
EXPOSE 5000 → Opens port
CMD → Runs application
🧑‍💻 2. Source Control Layer (GitHub)
🔧 Git Commands
git init
git add .
git commit -m "Initial commit - Flask Jenkins Pipeline"
git remote add origin https://github.com/<username>/<repo>.git
git branch -M main
git push -u origin main

🧠 Explanation
git init → Initializes repository
git add . → Stages files
git commit → Saves snapshot
git remote add → Links GitHub repo
git push → Uploads code
⚠️ GitHub PAT Issue
Problem:
Authentication failed

Solution:
Use Personal Access Token (PAT) instead of password
🔐 3. Jenkins Security & Connectivity
🌐 If Jenkins is Local

Use:

ngrok http 8080


👉 Example Output:

https://abc123.ngrok.io

🔑 Add GitHub PAT to Jenkins

Steps:

Manage Jenkins → Credentials
Add Credentials
Select:
Username: GitHub username
Password: PAT
⚙️ 4. Jenkins Pipeline (Jenkinsfile)
📄 Create Jenkinsfile
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

🧠 Explanation (Very Important)
🔹 pipeline {}

Defines the complete CI/CD workflow

🔹 agent any
Runs pipeline on any available Jenkins node
🔹 triggers { githubPush() }
Enables automatic trigger from GitHub
🔹 stages {}

Divides pipeline into steps

🔹 steps {}

Actual commands executed

📌 Stage-wise Explanation
1. Checkout
Pulls code from GitHub
2. Install
Creates virtual environment
Installs dependencies
3. Test
Runs pytest
Validates code
4. Build Docker Image
Creates Docker container image
🔹 post {}

Runs after pipeline execution

success → if build passes
failure → if build fails
always → always executes
🔗 5. GitHub Webhook Configuration
Steps
Go to:
Repository → Settings → Webhooks

Click Add Webhook
⚙️ Configuration
Payload URL:
http://<jenkins-url>/github-webhook/

Content Type:
application/json

Event:
✔️ Just the push event
⚙️ 6. Jenkins Job Configuration
Create Pipeline Job
Scroll to Build Triggers
Enable:

✔️ GitHub hook trigger for GITScm polling

🎬 7. Real-Time Demonstration
Step 1: Modify Code
return "Hello, Auto Trigger Working!"

Step 2: Push Changes
git add .
git commit -m "Testing webhook trigger"
git push origin main

Step 3: Observe Jenkins

✅ Build starts automatically
✅ No manual trigger

⚠️ 8. Troubleshooting Guide
Problem	Cause	Solution
Build not triggered	Webhook not working	Check GitHub Webhook logs
Jenkins not reachable	Localhost issue	Use ngrok
Docker permission denied	Jenkins not in docker group	usermod -aG docker jenkins
pytest not found	venv not activated	Activate before running
GitHub auth failed	Wrong credentials	Use PAT
Webhook red status	Incorrect URL	Fix /github-webhook/
🚀 Final Outcome

After completing this lab:

✅ Automated CI Pipeline
✅ GitHub → Jenkins integration
✅ Auto-trigger on push
✅ Docker image creation
