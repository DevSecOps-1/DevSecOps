# Application Deployment in OpenShift (PFC)

This repository contains YAML configurations for deploying a sample application in an OpenShift cluster. The configuration includes Project (Namespace), DeploymentConfig, Service, Route, ConfigMap, Secret, PersistentVolumeClaim, and Horizontal Pod Autoscaler.

## Prerequisites

- OpenShift CLI (oc) installed
- Access to an OpenShift cluster
- Sufficient permissions to create resources in the OpenShift cluster

## Deployment Steps

### 1. Create Project (Namespace)

command: ***oc apply -f project.yaml***

### project.yaml:
```sh
apiVersion: project.openshift.io/v1
kind: Project
metadata:
  name: my-app-project
```
### 2. Create DeploymentConfig

command: ***oc apply -f deploymentconfig.yaml***

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

command: ***oc apply -f service.yaml***

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

command: ***oc apply -f route.yaml***

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

command: ***oc apply -f configmap.yaml***

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
command: ***oc apply -f secret.yaml***

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

command: ***oc apply -f pvc.yaml***
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

command: ***oc apply -f hpa.yaml***

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

=================================== **Rollout** ============================================

A rollout updates the deployment to a new version. This can be done by changing the image or configuration.

### Update DeploymentConfig

To update the deployment, modify the ***image*** field in the ***DeploymentConfig***:

**Apply the changes**:

command: ***oc apply -f deploymentconfig.yaml***

### Monitor Rollout
You can monitor the rollout status using:
command: ***oc rollout status dc/my-app-deployment -n my-app-project***


======================================= **Rollback** =========================================

A rollback reverts the deployment to a previous version.

View Previous Rollouts

List the rollout history to find the revision number you want to roll back to:

command: ***oc rollout history dc/my-app-deployment -n my-app-project***

This command will display a list of revisions. For example:

```sh
deploymentconfigs.apps.openshift.io "my-app-deployment"
REVISION        STATUS          CAUSE
1               Complete        config change
2               Complete        config change
```

### Rollback to a Specific Revision

Rollback to a specific revision using the oc rollout undo command:

***oc rollout undo dc/my-app-deployment --to-revision=1 -n my-app-project***

Alternatively, you can rollback to the previous revision:

***oc rollout undo dc/my-app-deployment -n my-app-project***

**Note:**
- This setup provides a basic but comprehensive deployment of an application in an OpenShift cluster. we can expand and customize it further based on project specific application requirements.
===================================================================================

### Required DevOps component, including dependencies.
Setting up a DevOps pipeline involves configuring various components and tools to automate and manage the software development lifecycle. Below are the instructions for setting up key DevOps components, including their dependencies:

### 1. Version Control System (VCS)

## Git

### Dependencies:

- None, but a remote repository (e.g., GitHub, GitLab, Bitbucket) is often used.

### 2. Continuous Integration/Continuous Deployment (CI/CD)

Component: ***Jenkins, GitLab CI, GitHub Actions, Tekton***

**Dependencies:**

- CI/CD tool (Jenkins, GitLab Runner, etc.)
- Access to code repositories

### 3. Containerization and Orchestration

Component: ***Docker, Kubernetes, OpenShift***

**Dependencies:**
- Docker Engine or Docker Desktop
- Kubernetes or OpenShift cluster


### 4. Monitoring and Logging
Component: ***Prometheus, Grafana, ELK Stack***

**Dependencies:**
Monitoring and logging tools (Prometheus, Grafana, Elasticsearch)


### 5. Infrastructure as Code (IaC)
Component: **Terraform, Ansible**

**Dependencies:**
- IaC tools (Terraform, Ansible)
- Access to cloud providers or infrastructure APIs


### 6. SonarQube
SonarQube is used for continuous inspection of code quality and security.

**Dependencies:** 
- Java (JDK 11 or newer)


### 7. OWASP (Open Web Application Security Project)
OWASP provides tools and resources for improving software security. Key tools include OWASP Dependency-Check and OWASP ZAP.

**Dependencies:** 
- Java (JDK 8 or newer)


### 3. Nexus Repository
Nexus Repository is used for managing artifacts and dependencies.

**Dependencies:** 
- Java (JDK 11 or newer)

***Summary:**
1. Set Up VCS: Install Git, configure repositories.
2. CI/CD: Install and configure Jenkins, GitLab CI, or GitHub Actions.
3. Containerization: Install Docker, build and push images, deploy using Kubernetes/OpenShift.
4. Monitoring/Logging: Install Prometheus, Grafana, ELK Stack; set up dashboards.
5. IaC: Install Terraform and Ansible, write and apply configurations.
6. SonarQube: Configure SonarQube for code quality analysis. Integrate with Jenkins or GitHub Actions for automated code quality checks.
7. OWASP: Use OWASP tools like Dependency-Check for dependency vulnerabilities and ZAP for security scans. Integrate these tools into


