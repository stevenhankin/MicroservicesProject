# REST API Feed
Part of Udagram Image Filtering Microservice

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

## Requirements
* Node v10
* Docker Desktop

## Local Setup

## Docker Container Setup
Create image from codebase:
```
docker build -t biggyboss/udacity-restapi-user .
```
Initialise env vars:
```
export POSTGRESS_USERNAME=udagramstevedev
export POSTGRESS_PASSWORD=
export POSTGRESS_DB=udagramstevedev
export POSTGRESS_HOST=udagramstevedev.c6gqmqzlmiij.eu-west-2.rds.amazonaws.com
export AWS_REGION=eu-west-2a
export AWS_PROFILE=
export AWS_BUCKET=rain-drop-bucket
export URL='http://localhost:4200'   # URL of front-end server for enabling CORS 
export JWT_SECRET=1123344
```
Start container from image:
```
docker run --publish 8080:8080 \
           --rm \
           --name user \
           -e POSTGRESS_USERNAME=$POSTGRESS_USERNAME  \
           -e POSTGRESS_PASSWORD=$POSTGRESS_PASSWORD  \
           -e POSTGRESS_DB=$POSTGRESS_DB  \
           -e POSTGRESS_HOST=$POSTGRESS_HOST  \
           -e URL=$URL  \
           -e JWT_SECRET=1123344  \
           biggyboss/udacity-restapi-user
```

