# MicroservicesProject



## Installation
Setup environment variables for configuring containers to connect to PostGres RDS (which must be pre-configured and running on AWS)
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

```
cd udacity-c3-deployment/docker
docker-compose -f docker-compose-build.yaml build --parallel
docker-compose -f docker-compose-build.yaml push
docker-compose up
```


http://steve-bucket-project-1.s3-eu-west-2a.amazonaws.com
http://steve-fs-dev.s3-eu-west-2a.amazonaws.com


## Cluster Setup (EKS)
First, create a new cluster:
```
eksctl create cluster \
--name cluster-udagram \
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



## Shutdown of services when not in use
Need to do this to avoid unexpected charges
#### Delete cluster
```
kubectl delete daemonsets,replicasets,services,deployments,pods,rc   #maybe don't do --all
```
#### Shutdown database
aws rds stop-db-instance --db-instance-identifier udagramstevedev


