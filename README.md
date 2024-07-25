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

