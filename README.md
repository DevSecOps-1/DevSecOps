# OpenShift Application Deployment

This repository contains YAML configurations for deploying a sample application in an OpenShift cluster. The configuration includes Project (Namespace), DeploymentConfig, Service, Route, ConfigMap, Secret, PersistentVolumeClaim, and Horizontal Pod Autoscaler.

## Prerequisites

- OpenShift CLI (oc) installed
- Access to an OpenShift cluster
- Sufficient permissions to create resources in the OpenShift cluster

## Deployment Steps

### 1. Create Project (Namespace)

### command: oc apply -f project.yaml

### project.yaml:
```sh
apiVersion: project.openshift.io/v1
kind: Project
metadata:
  name: my-app-project
```
### 2. Create DeploymentConfig

### command: oc apply -f deploymentconfig.yaml

### deploymentconfig.yaml
```sh
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: my-app-deployment
  namespace: my-app-project
spec:
  replicas: 3
  selector:
    app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app-image:latest
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        env:
        - name: ENV_VAR_NAME
          value: "value"
  triggers:
  - type: ConfigChange
  - type: ImageChange
    imageChangeParams:
      automatic: true
      containerNames:
      - my-app-container
      from:
        kind: ImageStreamTag
        name: my-app-image:latest
```

3. Create Service

oc apply -f service.yaml

service.yaml:

apiVersion: v1
kind: Service
metadata:
  name: my-app-service
  namespace: my-app-project
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP


4. Create Route

oc apply -f route.yaml

route.yaml:
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: my-app-route
  namespace: my-app-project
spec:
  host: my-app.example.com
  to:
    kind: Service
    name: my-app-service
  port:
    targetPort: 80

5. Create ConfigMap (Optional)

oc apply -f configmap.yaml

configmap.yaml:

apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config
  namespace: my-app-project
data:
  config-key: config-value

