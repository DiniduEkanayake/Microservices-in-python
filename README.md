# Flask Microservice with Docker and Kubernetes Deployment

This project demonstrates how to build a microservice using Flask, containerize it with Docker, and deploy it to Kubernetes using Helm. The microservice architecture allows for loosely coupled, independently deployable services, making it a popular choice for modern applications.

## Introduction

Microservices architecture is a software design pattern where applications are built as a collection of loosely coupled, independently deployable services. Flask, a lightweight Python web framework, is widely used to build microservices due to its simplicity and flexibility.

In this guide, we will:

1. Build a Flask-based microservice.
2. Containerize it using Docker.
3. Deploy it to Kubernetes using Helm.

## Project Structure


## Building the Flask Microservice

### Install Dependencies

Create a virtual environment and install Flask:

```bash
python -m venv venv
source venv/bin/activate  # On Windows use: venv\Scripts\activate
pip install flask
```
Write the Flask Application (app.py):

```bash
from flask import Flask, jsonify

app = Flask(__name__)

@app.route("/")
def home():
    return jsonify({"message": "Hello, World!"})

@app.route("/health")
def health():
    return jsonify({"status": "UP"})

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```
Define Dependencies (requirements.txt):

```bash
flask
gunicorn
```
Dockerizing the Flask Microservice
- Create a Dockerfile
```bash
# Use official Python image
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Copy application files
COPY . .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose port
EXPOSE 5000

# Run the application
CMD ["gunicorn", "-b", "0.0.0.0:5000", "app:app"]
```
- Build and Run the Docker Container
```bash
# Build the Docker image
docker build -t flask-microservice .

# Run the container
docker run -p 5000:5000 flask-microservice
```
--Verify the service is running:
```bash
curl http://localhost:5000
```
### Deploying to Kubernetes

Create Kubernetes Deployment (k8s/deployment.yaml)

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-microservice
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-microservice
  template:
    metadata:
      labels:
        app: flask-microservice
    spec:
      containers:
      - name: flask-microservice
        image: flask-microservice:latest
        ports:
        - containerPort: 5000
```
Create Kubernetes Service (k8s/service.yaml)

```bash
apiVersion: v1
kind: Service
metadata:
  name: flask-microservice
spec:
  selector:
    app: flask-microservice
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
  type: LoadBalancer
```
Apply Kubernetes Configurations

```bash
kubectl apply -f k8s/
```
- Verify the deployment:
```bash
kubectl get pods
kubectl get services
```
### Helm Chart for Kubernetes Deployment

Create a Helm Chart
```bash
helm create flask-microservice
```
Define helm-chart/Chart.yaml
```bash
apiVersion: v2
name: flask-microservice
description: A Flask Microservice Helm Chart
type: application
version: 0.1.0
appVersion: "1.0"
```
Update helm-chart/values.yaml
```bash
replicaCount: 2

image:
  repository: flask-microservice
  tag: latest
  pullPolicy: IfNotPresent

service:
  type: LoadBalancer
  port: 80
```
Deploy with Helm
```bash
helm install flask-microservice helm-chart/
```
- Verify deployment:
```bash
helm list
kubectl get all
```
## Conclusion
We built a Flask microservice.
We containerized it using Docker.
We deployed it to Kubernetes.
We used Helm for easy deployment and management.
