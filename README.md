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

### 3. Create Service

### command: oc apply -f service.yaml

### service.yaml:
```sh
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
```

### 4. Create Route

### command: oc apply -f route.yaml

### route.yaml:
```sh
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
```
### 5. Create ConfigMap (Optional)

### command: oc apply -f configmap.yaml

### configmap.yaml:
```sh
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config
  namespace: my-app-project
data:
  config-key: config-value
```

### 6. Create Secret (Optional)
### command: oc apply -f secret.yaml

### secret.yaml:
```sh
apiVersion: v1
kind: Secret
metadata:
  name: my-app-secret
  namespace: my-app-project
type: Opaque
data:
  secret-key: c2VjcmV0LXZhbHVl  # base64 encoded value
```

### 7. Create PersistentVolumeClaim (Optional)

### oc apply -f pvc.yaml
### pvc.yaml:
```sh
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-app-pvc
  namespace: my-app-project
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### 8. Create Horizontal Pod Autoscaler (Optional)

### command: oc apply -f hpa.yaml
### hpa.yaml:
```sh
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
  namespace: my-app-project
spec:
  scaleTargetRef:
    apiVersion: apps.openshift.io/v1
    kind: DeploymentConfig
    name: my-app-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
```

### Conclusion:

This setup provides a basic but comprehensive deployment of an application in an OpenShift cluster. You can expand and customize it further based on your specific application requirements.