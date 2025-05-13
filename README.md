# dockercontent
Let's develop a **Python Flask application** that demonstrates **Docker content management** operations — like handling **volumes**, **files**, and **logs** — all inside a container.

We'll build an app that:

✅ Writes content to a file (demonstrating volume persistence)
✅ Reads the file to show the saved content
✅ Displays logs (showing how Docker logs work)

Then we’ll create a Docker container for it and run with a **mounted volume**.

---

## 🛠️ 1. Folder Structure

```
docker-content-app/
├── app.py
├── requirements.txt
├── Dockerfile
```

---

## 📄 app.py

```python
from flask import Flask, request, jsonify
import os

app = Flask(__name__)

DATA_FILE = '/app/data/content.txt'

# Ensure the directory exists
os.makedirs('/app/data', exist_ok=True)

@app.route('/')
def index():
    return "Docker Content Management Demo"

@app.route('/write', methods=['POST'])
def write_content():
    data = request.json.get('content', '')
    with open(DATA_FILE, 'a') as f:
        f.write(data + '\n')
    app.logger.info(f"Written content: {data}")
    return jsonify({"message": "Content written!"})

@app.route('/read', methods=['GET'])
def read_content():
    if not os.path.exists(DATA_FILE):
        return jsonify({"content": ""})
    with open(DATA_FILE, 'r') as f:
        content = f.read()
    return jsonify({"content": content})

@app.route('/logs', methods=['GET'])
def logs():
    return jsonify({"log_hint": "Use `docker logs <container_name>` to see logs"})

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
```

---

## 📄 requirements.txt

```
Flask
```

---

## 🐳 Dockerfile

```Dockerfile
# Stage 1: Base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy files
COPY . .

# Install Flask
RUN pip install -r requirements.txt

# Create a directory for volume data
RUN mkdir -p /app/data

# Expose Flask port
EXPOSE 5000

# Run the app
CMD ["python", "app.py"]
```

---

## ▶️ 2. Build and Run with Docker Commands

### 🔨 Build the image

```bash
docker build -t content-demo .
```

---

### 📦 Run the container with a volume

```bash
docker run -d -p 5000:5000 --name content-container -v content-data:/app/data content-demo
```

This:

* Mounts a Docker **volume** named `content-data`
* Exposes port `5000`

---

## 🧪 3. Test the App

### 📝 Write content

```bash
curl -X POST http://localhost:5000/write -H "Content-Type: application/json" -d "{\"content\": \"Hello Docker Volume!\"}"
```

### 📖 Read content

```bash
curl http://localhost:5000/read
```

### 🪵 View container logs

```bash
docker logs content-container
```

---

## 🧹 4. Manage Content Using Docker

### 📂 List volumes

```bash
docker volume ls
```

### 🔍 Inspect volume

```bash
docker volume inspect content-data
```

### ❌ Remove everything

```bash
docker stop content-container
docker rm content-container
docker volume rm content-data
```

---

## ✅ Summary

This app demonstrates Docker **content management** through:

* **Volume use** for persistent file storage
* **Log management**
* Basic **CRUD operations** inside a container

---

Would you like:

* A **GUI version** of this app?
* To extend it with file upload/download?
* Or convert this to a **multi-container app using Docker Compose**?

Let me know and I’ll help you build it!
