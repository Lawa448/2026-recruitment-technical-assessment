# Docker & Kubernetes Assessment Report

> [!TIP]
> Use this document to explain your design choices, optimisations and any challenges you faced.

## Dockerfile

#Motivations Image size FROM node:20-bookworm-slim Using small-sized official base image for node which decreases image size for less storage space and quicker image transfer. bookworm-slim is a linux based system based on Debian.

Caching image layers Taking advantage of the docker cache we don't want to recrate npm install dependencies layer even if the app file changes only when package.json file changes. So we copy the package.json files first, install its dependencies then we can copy the app files. The order from most frequently changed to least changed optimisises the image building time as it uses the cache in each build

Multi-stage building By using two stages for building and production artifacts from the build stages specifically the dev tools such as linting and typescript are discarded during runtime. At runtime we start with a fresh node base image and only copy the runtime files such as the compiled Javascript output and production dependencies in package.json. This overall produces a smaller image that only contains necessary build components, increasing download speed and security.
### Forked repository

https://github.com/Lawa448/academic-calendar-api

## Kubernetes

To runt he local clusteres on my machine I used minikube to run my Kubernetes, then I apply it to the navidrome folder by running "kubectl apply -f navidrome/", which creates everything defined in the YAML files. 
After creating the cluster I checked the status by running "kubectl get pods". To test on my local server I had to port forward which connects my machine with the Service. So when I make calls it gets recieved through the tunnel to the Navidrome inside my container.

Challenges
- Pods being stuck in pending, when I first ran the port-forwarding it failed because the Navidrome pod was not yet running. I used "kubectl get pods" to check the status and confirmed it was still pulling the image.
- Understanding how to translate the provided Docker Compose configuration into Kubernete manifests was difficult as the fields didn't have the same labels to the Kubernetes equivalent. 
- In the Docker localhost access is automatic however in Kubernetes the Service handles the interations between the containers internally. So I had to use port-forward to directly expose it


Configurations that are interesting (?)

- One of the challenges was trying to understand how the storage would be held when the pods restart and if they were to lost if it were to reset. I couldn't really find a solution so I set the volumers to emptyDir for temporary storage instead.