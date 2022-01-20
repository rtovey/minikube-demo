# Minikube Demo
Kubernetes/Minikube demo of a two tier web application

## Initial Setup

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

## Deploy Sample web application to the cluster

### Architecture

```
   +---------------+
   |               |
   | User (browser)|
   |               |
   +---------------+
           ^
           |
+----------+--------------------------+
|       Svc|        Kubernetes Cluster|
|  +-------+-------+                  |
|  |               | Pod 1            |
|  |    Web App    |                  |
|  |               |                  |
|  |  (astro-web)  | HPA auto-scaling |
|  |               | ---------->      |
|  +---------------+                  |
|          ^                          |
|          |                          |
|       Svc|                          |
|          |                          |
|  +-------+-------+                  |
|  |               | Pod 2            |
|  |    Data API   |                  |
|  |               | HPA auto-scaling |
|  |  (astro-api)  | ---------->      |
|  |               |                  |
|  +---------------+                  |
|                                     |
+-------------------------------------+
```

### Deployment Steps

1. Ensure Minikube is up: 
```
$ minikube status

# Start if required:
$ minikube start
```

2. Ensure `metrics-server` add-on is enabled (used to inform HPA):
```
$ minikube addons list

# Enable if required
$ minikube addons enable metrics-server
``` 

3. Apply deployment:
```
$ kubectl apply -f .
```

4. Check running pods:
```
$ kubectl get pods
```

## HPA auto-scaling demonstration

1. Install [drill](https://github.com/fcsonline/drill) (HTTP REST API benchmarking tool), if not already installed
```
$ sudo apt install cargo libssl-dev
$ cargo install drill
```

2. Ensure the sample web application has been deployed
```
$ kubectl get deployments
$ kubectl get pods
```

3. Look-up the exposed service for the app layer
```
$ minikube service astro-api
```

4. Copy/paste exposed service URL to `load-test/benchmark.yaml` file

5. Watch the HPA status
```
$ watch -n 1 kubectl get hpa
```

6. In a separate terminal window, run top and sort by process name ascending:
```
$ top

# Sort by COMMAND ascending:
# Press 'F', select "COMMAND", 's', 'Esc', 'R'
```

7. In a separate terminal window, tail the combined pod log streams:
```
$ kubectl logs -f -l run=astro-api --max-log-requests 10
```

8. In a separate terminal window, start the load test:
```
$ drill --benchmark load-test/benchmark.yaml
```

9. Expected results:
    1. The number of nodes should steadly increase
    2. The number of running processes should also increase in step 
    3. Requests should begin to get load balanced across the pods
    4. The CPU load per process should even out
    5. The API latency should reduce
    6. After the API load has stopped, the number of nodes and running process should gradually decrease back to the starting number (this may take several minutes to avoid choppy resource usage)

### Links

* [Kubernetes HPA scaling algorith details](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
* [Kubernetes HPA algorithm explanation](https://medium.com/expedia-group-tech/autoscaling-in-kubernetes-why-doesnt-the-horizontal-pod-autoscaler-work-for-me-5f0094694054)
