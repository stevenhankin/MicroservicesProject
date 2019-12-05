# Microservices Project

This project demonstrates the setup of application components as 
microservices (with separated concerns) running on a Cloud Infrastructure

This example runs on Amazons Elastic Kubernetes service

## Requirements
* Docker Desktop
* aws cli 1.16.257+
* kubectl 1.14.8
* eksctl 0.9.0


## Local Installation
Setup environment variables for configuring containers to connect to PostGres RDS (which must be pre-configured and running on AWS):
```
export POSTGRESS_USERNAME="udagramstevedev"
export POSTGRESS_PASSWORD="rkr1KFnaN3665NeHFgZ4"
export POSTGRESS_DB="udagramstevedev"
export POSTGRESS_HOST="udagramstevedev.c6gqmqzlmiij.eu-west-2.rds.amazonaws.com"
export AWS_REGION="eu-west-2"
export AWS_PROFILE="default"
export AWS_BUCKET="steves-fs-dev"
export JWT_SECRET="3468793474365432"
```
Now launch the multi-container Docker application:
```
cd udacity-c3-deployment/docker
docker-compose -f docker-compose-build.yaml build --parallel
docker-compose -f docker-compose-build.yaml push
docker-compose up
```
It should now be running as a local Docker Application


## Cluster Setup (EKS)
First, create a new cluster:
```
eksctl create cluster \
--name cluster-udagram-6-dec \
--version 1.14 \
--region eu-west-2 \
--nodegroup-name standard-workers \
--node-type t2.micro \
--nodes 3 \
--nodes-min 1 \
--nodes-max 4 \
--node-ami auto
```
Then configure your kubectl client to 
```
#aws eks --region eu-west-2 update-kubeconfig --name cluster-dev
```


## Build Docker images and start using Docker Compose
cd MicroservicesProject/udacity-c3-restapi-user
docker build . -t biggyboss/udacity-restapi-user

cd /MicroservicesProject/udacity-c3-frontend
docker build . -t biggyboss/udacity-frontend

cd MicroservicesProject/udacity-c3-deployment/docker
docker-compose up



## Deployment
Publish deployments and services to cluster

First, navigate to kubernetes setup folder:
```
cd udacity-c3-deployment/k8s
```

Encode your AWS credentials and substitute the placeholder
in the Secret YAML when applying.  This is so that your
credentials are not checked into public github:
``` 
sed  "s/___.*/`cat ~/.aws/credentials | base64 -`/" aws-secret.yaml | kubectl apply -f -
```

Deploy the config / containers / services:
```Shell
kubectl apply -f env-configmap.yaml 
kubectl apply -f backend-feed-service.yaml	
kubectl apply -f backend-user-service.yaml	
kubectl apply -f frontend-service.yaml		
kubectl apply -f reverseproxy-service.yaml
kubectl apply -f backend-feed-deployment.yaml
kubectl apply -f backend-user-deployment.yaml 
kubectl apply -f frontend-deployment.yaml
kubectl apply -f reverseproxy-deployment.yaml 
```

Also apply the env secret (which I've put above and outside of the Git Repo)
```
kubectl apply -f ../../../env-secret.yaml 
```

Once it is running, check the current service:
```Shell
kubectl get svc frontend
```

The External-IP can be used in a browser, with port :8100 appended to access the Front End 👏🏻


## Shutdown of services when not in use
Need to do this once testing is finished to avoid unexpected charges! 😭
#### Delete cluster
```
kubectl delete daemonsets,replicasets,services,deployments,pods,rc   #maybe don't do --all
```
#### Shutdown database
aws rds stop-db-instance --db-instance-identifier udagramstevedev


