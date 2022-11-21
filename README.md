# Canary deployment demo
Canary deployments are a pattern for rolling out releases to a subset of users or servers. The idea is to first deploy the change to a small subset of servers, test it, and then roll the change out to the rest of the servers. Once the application is approved for release, only a limited number of users are routed to it, thereby minimising its impact. If there are no fails, the new version is rolled out gradually to the entire infrastructure.

## Canary deployment strategies in Kubernetes
![canary-deploy-processes](Canary-deployment.jpeg "Canary-deployment")

## Step 1: Deploy V1
Release application in version 1, use image nginx：1.22，replicas is 3 
- Create Namespace
- Create Deployment
- Create Service for external access
```
kubectl apply -f deploy-1.yaml
kubectl get all -n dev
```
## Step 2： Create Canary deployment
Release new version, use image docker/getting-started, replicas is 1
```
kubectl apply -f deploy-canary.yaml
kubectl get all -n dev
```

## Step 3: Expand the scope of new applications
```
kubectl scale deploy nginx-deployment-canary --replicas=3 -n=dev
kubectl get all -n dev
```

## Step 4: Offline the old version application
```
kubectl scale deploy nginx-deployment-v1 --replicas=0 -n=dev
kubectl get all -n dev
```

## Limitations:
-Traffic distribution cannot be performed based on user registration time, region and other information
- If the same user calls the service multiple times, it is possible to request the pod of the old version for the first time and pid of the new verison for the second time
