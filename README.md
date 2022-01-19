# minikube-demo
Kubernetes/Minikube demo of a two tier web application

# Initial Setup

1. Install Linux development environment
    1. e.g. Ubuntu 20.4 server
    2. On Windows 11 with Hyper-V virtualisation
2. Install [minikube](https://minikube.sigs.k8s.io/docs/start/) (cut-down standalone version of Kubernetes for learning and exploring Kubernetes features on a single node cluster)

3. Update package manager and system
```
$ sudo apt update
$ sudo apt upgrade
$ sudo reboot
```

4. Install docker deamon; check version and add user to docker group
```
$ sudo apt install docker.io
$ sudo usermod -aG docker <current username>
```

5. Logout and in to apply group update
```
$ exit
... login ...
```

6. Check docker version and daemon is up
```
$ docker --version
$ docker ps
```

7. Download and install Minikube deb package (for Ubuntu x86)
```
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
$ sudo dpkg -i minikube_latest_amd64.deb
```

8. Start Minikube
```
$ minikube start
```

9. Install Minikube kubectl command
```
$ minikube kubectl
```

10. Map kubectl command to Minikube and check
```
$ sudo ln -s $(which minikube) /usr/local/bin/kubectl
$ kubectl get pods
```

# Deploy Cluster

1. Ensure Minikube is up: 
```
$ minikube start
```
2. Apply deployment:
```
$ kubectl apply -f .
```