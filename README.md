# kubernetes-bootcamp
# MongoDB and mongo-express Kubernetes Deployment and Service Configuration

This repository contains Kubernetes deployment and service configurations for deploying MongoDB and mongo-express. This setup enables you to quickly create a MongoDB database along with a web-based admin interface provided by mongo-express.

## Prerequisites

Before deploying this configuration, make sure you have the following:

- A running Kubernetes cluster.
- `kubectl` command-line tool configured to access your cluster.
- Docker images for `mongo` and `mongo-express` available in your container registry.
- Necessary secrets created for MongoDB root credentials (`mongodb-secret`) and configuration (`mongodb-configmap`).

## Deployment

### MongoDB Deployment

The following YAML represents the MongoDB deployment configuration:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017

mongo-express Deployment
The following YAML represents the mongo-express deployment configuration:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express
  labels:
    app: mongo-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
      - name: mongo-express
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom: 
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer  
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000

Deploying the Configuration
To deploy the configurations to your Kubernetes cluster, use the following commands:

Apply the MongoDB configuration:
kubectl apply -f mongodb-deployment.yaml
kubectl apply -f mongodb-service.yaml

Apply the mongo-express configuration:
 kubectl apply -f mongo-express-deployment.yaml
 kubectl apply -f mongo-express-service.yaml

Accessing Services:

markdown
Copy code
# MongoDB and mongo-express Kubernetes Deployment and Service Configuration

This repository contains Kubernetes deployment and service configurations for deploying MongoDB and mongo-express. This setup enables you to quickly create a MongoDB database along with a web-based admin interface provided by mongo-express.

## Prerequisites

Before deploying this configuration, make sure you have the following:

- A running Kubernetes cluster.
- `kubectl` command-line tool configured to access your cluster.
- Docker images for `mongo` and `mongo-express` available in your container registry.
- Necessary secrets created for MongoDB root credentials (`mongodb-secret`) and configuration (`mongodb-configmap`).

## Deployment

### MongoDB Deployment

The following YAML represents the MongoDB deployment configuration:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
mongo-express Deployment
The following YAML represents the mongo-express deployment configuration:

yaml
Copy code
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express
  labels:
    app: mongo-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
      - name: mongo-express
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom: 
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer  
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000
Deploying the Configuration
To deploy the configurations to your Kubernetes cluster, use the following commands:

Apply the MongoDB configuration:
shell
Copy code
kubectl apply -f mongodb-deployment.yaml
kubectl apply -f mongodb-service.yaml
Apply the mongo-express configuration:
shell
Copy code
kubectl apply -f mongo-express-deployment.yaml
kubectl apply -f mongo-express-service.yaml
Accessing Services
-MongoDB can be accessed through the mongodb-service service on port 27017.
-mongo-express can be accessed through the mongo-express-service service on port 8081. You can access the admin interface by navigating to http://<Cluster-IP>:8081 in your browser.

Cleaning Up:
To remove the deployed resources, use the following commands:
kubectl delete -f mongo-express-service.yaml
kubectl delete -f mongo-express-deployment.yaml
kubectl delete -f mongodb-service.yaml
kubectl delete -f mongodb-deployment.yaml

Remember to handle any associated resources or data as needed before deleting.

Replace `<Cluster-IP>` with the actual Cluster IP or LoadBalancer IP assigned by your Kubernetes environment. Make sure to also replace the placeholders like `<container-registry>` with actual values in your configuration files.

Note: Please verify and adapt this README file and configuration files according to your specific Kubernetes environment and requirements before deploying.

