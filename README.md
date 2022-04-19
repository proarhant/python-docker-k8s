# python-docker-k8s
Demo of a sample Python Flask web app running on Kubernetes.

The web application will be containerized in Docker first.
I will then deploy this Dockerized web app in K8s using LoadBalancer service. For the demo purpose, a minikube K8s single node cluster will be used.

# Python applicaiton in Flask framework
This is a sample python Flask application that displays both in `console and web browser` its current environment (e.g. Production, Development, Staging, etc) along with the port it listens to.

# Dir structure for this containerized application
```
[cloudadm@cloud98 python-docker-k8s]# tree -a
.
├── bigDummyFile.md
├── Dockerfile
├── .dockerignore
├── README.md
├── requirements.txt
└── source
    └── app.py

1 directory, 6 files
[cloudadm@cloud98 python-docker-k8s]#
```

# .dockerignore
In this demo, the `.dockerignore` will exclude all markdown files except README.md.

`bigDummyFile.md` is a dummy file created to show the validity of `.dockerignore`. 

We exclude files and directories by adding a `.dockerignore` file (by matchinng patterns in it) to the context directory. This increases the build’s 
performance, helps to avoid unnecessarily trasferring large or sensitive files and directories to images using `ADD` or `COPY`.

Feel free to adjust the command line options I used to create this 127M dummy file.

```
[cloudadm@cloud98 python-docker-k8s]# dd if=/dev/zero of=./bigDummyFile.md bs=4k iflag=fullblock,count_bytes count=127M
32512+0 records in
32512+0 records out
133169152 bytes (133 MB) copied, 0.194471 s, 685 MB/s
```

# Run locally
```
git clone https://github.com/proarhant/python-docker-k8s.git
cd python-docker-k8s
pip3 install -r requirements.txt
python3.7 ./source/app.py -e production --host 0.0.0.0:5678
```

Please note the example command to execute the app used my local environment whcih has `Python 3.7` installed.

Both the `console` and `web browser` will display the output from the app. THe screen capture below shows such results from the app excuted locally.

![image](https://user-images.githubusercontent.com/2681229/163913987-8b9714d6-6168-4a5f-b7f6-dae05a9c56ae.png)


# Build & Run examples in Docker
Lets get the app running and tested within less than a minute!

```
git clone https://github.com/proarhant/python-docker-k8s.git
cd python-docker-k8s
docker build --tag flask-docker .
docker run --name flask-app -p 5000:5000 flask-docker
curl localhost:5000
```

The demo code facilitates to choose values for the port and mode of environement.
The two arguements in `docker run` command for this demo code will override `container port` and `environment` of the web app e.g. `-e production --host 0.0.0.0:8888`

```
docker run --name testCont -p 8888:8888 flask-docker -e production --host 0.0.0.0:8888 
docker run -p 8888:8888 flask-docker -e staging --host 0.0.0.0:8888
curl localhost:8888
```

The snippets of the commands used for the demo and screenshots displaying the output from the app are captured below.
```
[cloudadm@cloud98 python-docker-k8s]# docker build --tag flask-docker .
Sending build context to Docker daemon   7.68kB
Step 1/7 : FROM python:3.9-slim-buster
 ---> 0c393f29306f
Step 2/7 : WORKDIR /app
 ---> Running in 50829f2ec91f
Removing intermediate container 50829f2ec91f
 ---> cae92f6e5330
Step 3/7 : COPY requirements.txt requirements.txt
 ---> b9f2b366ba63
Step 4/7 : RUN pip3 install -r requirements.txt
 ---> Running in 164a0f9ab8ca
Collecting Flask
  Downloading Flask-2.1.1-py3-none-any.whl (95 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 95.2/95.2 KB 10.9 MB/s eta 0:00:00
....
....
....
Step 5/7 : COPY . .
 ---> cc61f46a5aaa
Step 6/7 : ENTRYPOINT ["python3", "/app/source/app.py"]
 ---> Running in 1cb5e8e463a4
Removing intermediate container 1cb5e8e463a4
 ---> 56dc1f9539e8
Step 7/7 : CMD ["-e", "development", "--host", "0.0.0.0:5000"]
 ---> Running in bd067253a679
Removing intermediate container bd067253a679
 ---> 879a63532045
Successfully built 879a63532045
Successfully tagged flask-docker:latest
[cloudadm@cloud98 python-docker-k8s]# docker tag flask-docker:latest flask-docker:v1.0.0
[cloudadm@cloud98 python-docker-k8s]# docker images
REPOSITORY     TAG               IMAGE ID       CREATED          SIZE
flask-docker   latest            879a63532045   8 seconds ago    129MB
flask-docker   v1.0.0            879a63532045   8 seconds ago    129MB
python         3.9-slim-buster   0c393f29306f   4 days ago       118MB
[cloudadm@cloud98 python-docker-k8s]# 

[cloudadm@cloud98 python-docker-k8s]# docker run --name flask-app -p 5000:5000 flask-docker
Current environment :  development  Interface:  0.0.0.0  Port:  5000
 * Serving Flask app 'app' (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on all addresses (0.0.0.0)
   WARNING: This is a development server. Do not use it in a production deployment.
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000 (Press CTRL+C to quit)
172.17.0.1 - - [18/Apr/2022 19:42:24] "GET / HTTP/1.1" 200 -
103.239.77.70 - - [18/Apr/2022 19:42:48] "GET / HTTP/1.1" 200 -
103.239.77.70 - - [18/Apr/2022 19:42:49] "GET /favicon.ico HTTP/1.1" 404 -
172.17.0.1 - - [18/Apr/2022 19:43:50] "GET / HTTP/1.1" 200 -
103.239.77.70 - - [18/Apr/2022 19:46:36] "GET / HTTP/1.1" 200 -
35.85.153.204 - - [18/Apr/2022 19:52:10] "GET / HTTP/1.1" 200 -


[cloudadm@cloud98 ~]# curl localhost:5000
Current environment is >>> development <<< listening on [0.0.0.0:5000]
[cloudadm@cloud98 ~]#

```
![image](https://user-images.githubusercontent.com/2681229/163869040-ae781125-778f-4fea-a277-135a9489fc7c.png)
![image](https://user-images.githubusercontent.com/2681229/163870888-b62e1892-3402-4f26-877e-b6abbf0f3f0f.png)

The application also displays the output in the browser e.g. http://127.0.0.1:5000 
