# Docker

## Q&A


## Notes

- Alternatives to Docker are:
    - rkt (which also works with Kubernetes)
- Docker Hub:
    - Online service to store and fetch docker images
    - Allows you to build dockers images online
- Docker Benefits:
    - Isolation: You ship a binary with all the dependencies
    - Closer parity between dev, QA and production environments
    - Docker makes development teams able to ship faster
    - You can run the same docker image, unchanged, on laptops, data center VMs and Cloud providers
    - Docker uses Linux Containers (a kernel feature) for operating system-level isolation
- You should only run 1 process in one container
- All data created in a container is not preserved. When a container stops, all the changes within a container are lost
    - You can preserve data using volumes

## Simple Example of Dockerfile

FROM node.4.6
WORKDIR /app
ADD . /app
RUN npm install
EXPOSE 3000
CMD npm start

## Useful Docker Commands

- docker images (lists all docker images on your machine)
- docker build . (builds a docker image based on the current directory)
- docker build -t tagname:version . (builds a docker image based on the current directory and tags the image with a name and version)
- docker run -p containerport:exposeport -t dockerimageid

To Push Docker Images to Docker Hub:
- docker login
- You have to tag your image with your docker-login/image-name
    - docker tag imageid docker-login/image-name
- docker push docker-login/image-name

## Buzz Words & Terms
- 

## What is machine learning?

- 

## Resource Links

- [12-Factor App](https://12factor.net)