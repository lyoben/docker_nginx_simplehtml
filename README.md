# docker-nginx-simple-html

### Contents:

### Description
Introductory to deploy a web service as a Kubernetes pod. First by creating a nginx docker image. Then deploy Kubernetes pod and service to make the service available. Kubernetes implmentation using Minikube single node cluster on Ubuntu VM.

### Pre-requisite
Ubuntu 20.04 Desktop

### Installation
1. Install Docker by following Step 1 — Installing Docker and Step 2 — Executing the Docker Command Without Sudo in this [article](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04/).
2. Install Minikube by following Step 1- 5 in this [article](https://www.linuxtechi.com/how-to-install-minikube-on-ubuntu/)
3. Change

### Setting up
Create a folder
> mkdir -p ~/docker-nginx
>
> cd ~/docker-nginx

Create a static html page
```
cat << EOF > index.html
 <html>
  <head>
    <title>Docker nginx simple html</title>
  </head>

  <body>
    <div class="container">
      <h1>Welcome to Docker nginx simple html!</h1>
      <p> Visit <a href="url">https://github.com/lyoben</a> for more info.
      </p>
    </div>
  </body>
</html>
EOF
```

Create a dockerfile
```
cat << EOF > dockerfile
FROM ubuntu
RUN apt-get update
RUN apt-get install nginx curl -y
COPY index.html /var/www/html
EXPOSE 80
CMD ["nginx","-g","daemon off;"
EOF
```
Build docker image
> docker build -t simplehtml-nginx:v1 .
```
blow@abra:~/docker-nginx$ docker image ls
REPOSITORY                                           TAG       IMAGE ID       CREATED          SIZE
simplehtml-nginx                                     v1        bf85b8e3e951   49 minutes ago   184MB
---snipped---
```
Create pod definition file
```
cat << EOF > simplehtml-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    env: test
  name: simplehtml
spec:
  containers:
  - image: simplehtml-nginx:v1
    name: simplehtml
    imagePullPolicy: Never
EOF
```
Create service definition file
```
cat << EOF > simplehtml-svc.yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    env: test
  name: simplehtml-svc
spec:
  ports:
  - name: 80-80
    nodePort: 30080
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    env: test
  type: NodePort
EOF
```

