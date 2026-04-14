# 🚀 CI/CD Pipeline using GitHub Actions (Hands-on Lab)

## 📌 Objective

To understand and implement a **CI/CD pipeline** using GitHub Actions by:

* Running automated tests (CI)
* Building and pushing Docker images (CD)

---

# 🧩 PROGRAM 1: Basic CI Pipeline (Python + Pytest)

---

## 📁 Project Structure

```
ci-cd-demo-1/
│── app.py
│── test_app.py
│── requirements.txt
└── .github/
    └── workflows/
        └── ci.yml
```

---

## 🧾 Step 1: app.py

```python
def add(a, b):
    return a + b

if __name__ == "__main__":
    print(add(2, 3))
```

---

## 🧾 Step 2: test_app.py

```python
from app import add

def test_add():
    assert add(2, 3) == 5
```

---

## 🧾 Step 3: requirements.txt

```
pytest
```

---

## ⚙️ Step 4: GitHub Actions Workflow (ci.yml)

```yaml
name: CI Pipeline

on:
  push:
    branches: ["main"]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: pytest
```

---

## ▶️ How to Run

```bash
git init
git add .
git commit -m "Initial commit"
git push origin main
```

---

## ✅ Expected Output

```
collected 1 item
.
1 passed
```

---

## ❌ Common Errors (Program 1)

### 1. No tests found

```
collected 0 items
```

✔ Fix:

* File name → `test_app.py`
* Function name → `test_add()`

---

### 2. Wrong file structure

✔ Ensure:

```
.github/workflows/ci.yml
```

---

---

# 🧩 PROGRAM 2: CI/CD Pipeline (Docker Integration)

---

## 📁 Project Structure

```
ci-cd-demo-2/
│── app.py
│── requirements.txt
│── Dockerfile
└── .github/
    └── workflows/
        └── cd.yml
```

---

## 🧾 Step 1: app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "CI/CD Pipeline Working!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## 🧾 Step 2: requirements.txt

```
flask
```

---

## 🐳 Step 3: Dockerfile

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["python", "app.py"]
```

---

## ⚙️ Step 4: GitHub Actions Workflow (cd.yml)

```yaml
name: CI-CD Pipeline

on:
  push:
    branches: ["main"]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Login to Docker Hub
        run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

      - name: Build Docker Image
        run: docker build -t myapp:latest .

      - name: Tag Image
        run: docker tag myapp:latest ${{ secrets.DOCKER_USERNAME }}/myapp:latest

      - name: Push Image
        run: docker push ${{ secrets.DOCKER_USERNAME }}/myapp:latest

      - name: Deploy Step
        run: echo "Deploying application..."
```

---

# 🔐 GitHub Secrets Setup

Go to:

```
Settings → Secrets → Actions
```

Add:

| Name            | Value                |
| --------------- | -------------------- |
| DOCKER_USERNAME | your Docker username |
| DOCKER_PASSWORD | Docker access token  |

---

# ▶️ Execution Flow

1. Push code to GitHub
2. Pipeline runs automatically
3. Steps executed:

   * Build
   * Docker login
   * Push image

---

# ✅ Expected Output

```
✔ Login to Docker Hub
✔ Build Docker Image
✔ Push Image
✔ Deploy Step
```

---

# 🧪 Verification

## ✔ Check GitHub Actions

* All steps should be green ✅

## ✔ Check Docker Hub

* Image should be visible

## ✔ Run Locally

```bash
docker pull username/myapp
docker run -p 5000:5000 username/myapp
```

Open:

```
http://localhost:5000
```

Output:

```
CI/CD Pipeline Working!
```

---

# ❌ Common Errors (Program 2)

---

## 1. Docker Login Failure

### Error:

```
Must provide --username with --password-stdin
```

### Cause:

Secrets not set

### Fix:

* Add `DOCKER_USERNAME`
* Add `DOCKER_PASSWORD`

---

## 2. Empty Secrets Issue

### Error:

```
echo "" | docker login -u ""
```

### Cause:

Secrets not configured or wrong names

### Fix:

* Ensure exact names (case-sensitive)
* Re-add secrets

---

## 3. Repository Not Found

### Error:

```
denied: repository does not exist
```

### Fix:

* Create repo in Docker Hub
* Match naming

---

## 4. Wrong Token

### Error:

```
unauthorized
```

### Fix:

* Use Docker **Access Token**
* Not password

---

## 5. YAML Indentation Errors

### Cause:

Improper spacing

### Fix:

* Maintain proper indentation

---

# 🎓 Key Learning Outcomes

Students will understand:

* CI vs CD
* Automation using GitHub Actions
* Docker integration
* Secure credential management
* Debugging CI/CD pipelines

---

# 🧠 Instructor Tips

✔ Show success → failure → fix
✔ Demonstrate real debugging
✔ Emphasize naming conventions
✔ Highlight importance of secrets

---

# 🏁 Conclusion

This lab demonstrates a **complete CI/CD pipeline** from:

* Code commit → Testing → Build → Deployment

---

**End of Lab 🚀**
