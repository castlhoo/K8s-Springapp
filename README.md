
# 🚀 K8s-Springapp

This project demonstrates how to build a simple **Spring Boot application** as a Docker image and deploy it in a **Kubernetes** environment. It uses **Minikube** to run a local Kubernetes cluster and stores the Docker image in Docker Hub for management and distribution.

## 📝 Project Goal
The goal of this project is to deploy a **Spring Boot application** using **Kubernetes**, making it accessible via the **network**. Through this, we will explore Kubernetes' capabilities for **automated deployment**, scalability, and management.

---

## 📋 Project Steps

### 1. Creating and Building the Spring Project
First, we create a simple Spring Boot project using Maven, configuring the external server port to **9090**.

- Spring Boot project creation
  ![image](https://github.com/user-attachments/assets/f73a7a51-30fa-44ea-ad66-a4b3d71613ea)

- Building the project using Maven
  ![image](https://github.com/user-attachments/assets/64ee4635-81f4-40fe-94e8-a53556dc44ae)
  ![image](https://github.com/user-attachments/assets/9d43b1e7-605e-47d8-b76e-8e0d87e26ea5)

### 2. Creating Docker Image 🐳
We create a `Dockerfile` to build the Spring application into a Docker image, and then upload it to **Docker Hub** for management.

- Docker image creation process
  ![image](https://github.com/user-attachments/assets/b29129be-76c0-47a9-a1c4-a23e18b1f5d9)
  ![image](https://github.com/user-attachments/assets/1c318dcf-7ff2-4e42-a402-786fbd8d6e95)
  ![image](https://github.com/user-attachments/assets/3afc768b-6309-4152-b314-03885baa0b17)

### 3. Tagging and Uploading the Docker Image to Docker Hub 🔄
After building the image, we tag and upload it to Docker Hub so that other servers or clusters can use it.

- Tagging the image
  ![image](https://github.com/user-attachments/assets/a62bec19-dd7f-444a-951a-2ed9150853d7)

- Uploading the image to Docker Hub
  ![image](https://github.com/user-attachments/assets/d0583fe5-67f1-4357-8c0c-d4e57c6fc453)

### 4. Downloading the Image on Myserver02 and Deploying on Kubernetes
Now that the image is uploaded to Docker Hub, we download it on another virtual machine (Myserver02) and deploy it on Kubernetes.

- Starting the myserver02 VM
  ![image](https://github.com/user-attachments/assets/9a44f0ac-acdc-40ac-8a4a-88840c657223)

- Downloading the image from Docker Hub
  ![image](https://github.com/user-attachments/assets/26334b1f-d9f8-424f-80e7-c5ff00bc5fbe)

### 5. Installing and Setting Up Minikube ⚙️
We use Minikube to set up a local Kubernetes cluster. After downloading the Docker image, we deploy the Spring Boot application on the Kubernetes cluster.

- Installing and setting up Minikube
  ![image](https://github.com/user-attachments/assets/6a7bd992-ae8e-4c86-a798-c67568fceb17)
  ![image](https://github.com/user-attachments/assets/59db2f76-311b-4bcc-b6e5-d136046fb5d1)
  ![image](https://github.com/user-attachments/assets/dee8fcda-3b5e-47bd-a6bd-b1afcbfe9994)


### TroubleShooting 🦸
If you encounter disk space issues during Minikube installation or operation, you can free up space using the `docker system prune` command.
  ![image](https://github.com/user-attachments/assets/9e85b044-8f9c-450f-a609-cbb93efd3815)

- Disk space optimization
  ![image](https://github.com/user-attachments/assets/953bc5ef-9aeb-4d19-ab93-807d3b299684)

### 6. Configuring Kubernetes Deployment ⚡
We now configure a **YAML file** to deploy the Spring Boot application on the Kubernetes cluster.

### `java-app-deployment.yml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-app
  labels:
    app: java-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: java-app
  template:
    metadata:
      labels:
        app: java-app
    spec:
      containers:
      - name: java-app
        image: castlehoo/k8sjava-app:1.0  # Image downloaded from Docker Hub
        ports:
        - containerPort: 9090  # Port used by the Spring Boot application
---
apiVersion: v1
kind: Service
metadata:
  name: java-app-service
spec:
  selector:
    app: java-app
  ports:
  - protocol: TCP
    port: 88  # External port for access
    targetPort: 9090  # Internal port of the container
  type: LoadBalancer  # Enables external access to the service
```
### K8s-Springapp Deployment and Service YAML Explanation

This YAML configuration defines a **Kubernetes Deployment** and a **Service** for a Spring Boot application containerized using Docker. Below is an explanation of each section in detail:

#### Deployment Section

The `Deployment` resource manages the deployment of your Spring Boot application across multiple pods.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-app
  labels:
    app: java-app
```

- **apiVersion**: Specifies the version of the Kubernetes API being used. In this case, it's `apps/v1`, which supports Deployments.
- **kind**: Defines the type of Kubernetes resource, which is `Deployment` in this case.
- **metadata**: Contains metadata like the `name` of the deployment (`java-app`) and labels used to categorize or identify this deployment (`app: java-app`).

```yaml
spec:
  replicas: 3
  selector:
    matchLabels:
      app: java-app
```

- **spec**: Defines the specification for the deployment.
- **replicas**: Specifies how many instances (pods) of the application should be running. In this case, 3 replicas are deployed.
- **selector**: Specifies which pods the deployment should manage. Here, it selects pods with the label `app: java-app`.

```yaml
template:
  metadata:
    labels:
      app: java-app
  spec:
    containers:
    - name: java-app
      image: castlehoo/k8sjava-app:1.0  # Image downloaded from Docker Hub
      ports:
      - containerPort: 9090  # Port used by the Spring Boot application
```

- **template**: This defines the blueprint for the pods that will be created by this deployment.
- **metadata**: Labels the pods with `app: java-app`, so the service and deployment can manage them.
- **spec**: Defines the specification of the containers inside the pods.
  - **containers**: Lists the containers that will run inside each pod.
    - **name**: The name of the container is `java-app`.
    - **image**: Specifies the Docker image to use. The image `castlehoo/k8sjava-app:1.0` will be pulled from Docker Hub.
    - **ports**: Exposes port `9090` inside the container, where the Spring Boot application is running.

#### Service Section

The `Service` resource provides a stable network interface for the deployed application, making it accessible within or outside the cluster.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: java-app-service
```

- **apiVersion**: Specifies the version of the Kubernetes API, which in this case is `v1`, used for Service resources.
- **kind**: Defines the type of Kubernetes resource, which is `Service`.
- **metadata**: Contains the name of the service, `java-app-service`.

```yaml
spec:
  selector:
    app: java-app
  ports:
  - protocol: TCP
    port: 88  # External port for access
    targetPort: 9090  # Internal port of the container
  type: LoadBalancer  # Enables external access to the service
```

- **spec**: Defines the specification for the service.
  - **selector**: Matches pods with the label `app: java-app` to route traffic to those pods.
  - **ports**: Defines the port configuration:
    - **port**: The external port (`88`) that will be exposed to the outside world.
    - **targetPort**: The internal port (`9090`) on the container where the Spring Boot application is listening.
  - **type**: `LoadBalancer` exposes the service externally through a cloud provider's load balancer, making the application accessible from outside the Kubernetes cluster.


### 6. Deployment and Network Configuration 🌐
1. We use `kubectl` to apply the deployment file and configure a **Minikube tunnel** to allow external access to the application.

![image](https://github.com/user-attachments/assets/c709165a-1574-4fb0-ad92-714bff35bf0c)

2. Once the external network is assigned, we can access the application via **`10.104.224.199:88`**.

![image](https://github.com/user-attachments/assets/5ea6da67-d155-4306-87e7-f2929d4c9edf)
![image](https://github.com/user-attachments/assets/bbd1e80d-3b2d-4254-bf97-07f2a1b670fc)

---

## 🎯 Conclusion
This project focused on understanding how to deploy and manage a **Spring Boot application** in a **Kubernetes** environment. From building the Docker image, setting up Minikube for a local Kubernetes cluster, to deploying the application and configuring network access through Kubernetes, we explored key aspects of Kubernetes deployment.

This experience allowed us to work with a **cloud-native application deployment environment**, providing **scalability**, **resilience**, and **management efficiency**. The knowledge gained here forms the foundation for scaling to more complex systems.

---

This project includes core concepts of **DevOps** and will play a critical role in the future for building **cloud infrastructure** and **CI/CD pipelines**. 😊
