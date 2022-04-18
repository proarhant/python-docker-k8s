# python-docker-k8s
Demo of a sample Python Flask web app running on Kubernetes

# Python applicaiton in Flask framework
This is a sample python Flask application that displays its current environment (e.g. Production, Development, Staging, etc) along with the port it listens to.

# .dockerignore
All markdown files except README.md are excluded.

# Build & Run examples:
docker build --tag flask-docker .

docker run --name testCont -p 5000:5000 flask-docker
docker run --name testCont -p 8888:8888 flask-docker -e production --host 0.0.0.0:8888
docker run -p 8888:8888 flask-docker -e staging --host 0.0.0.0:8888
