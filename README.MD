**WORK IN PROGRESS**

This repository contains simple Kubernetes demonstrations. It can also serve as a reference for YAML manifests and resource usage. They have been tested on the Docker Desktop K8 cluster. See the README in each sub-directory for detailed instructions.

## Setting up Kubernetes in Docker

The Kubernetes server in Docker is a non-configurable, single-node cluster, ideal for learning Kubernetes and local testing. Follow the [official Docker guide](https://docs.docker.com/desktop/kubernetes/) to install and enable the Kubernetes server. When Kubernetes is running, an additional status bar should appear in the Docker Desktop application and the Docker menu display.

To verify that Kubernetes is running, and that `kubectl` is pointing to the correct context, run the following command:

    $ kubectl get nodes

The outpout should look something like this:

    NAME             STATUS   ROLES           AGE   VERSION
    docker-desktop   Ready    control-plane   22h   v1.29.1

Otherwise, `kubectl` may be pointing to another context. You can get the available contexts and switch to the `docker-desktop` context with the following commands:

    $ kubectl config get-contexts
    $ kubectl config use-context docker-desktop

## Demonstations

[Services](./service_demo/README.MD)

[Ingress](./ingress_demo/README.MD)

## About Me

Thanks for viewing my repo! This project is a way for me to gain practical experience with Docker and Kubernetes, whilst creating a knowledge base I can reference in the future. I still have a lot to learn, so your feedback is much appreciated.

[LinkedIn](https://www.linkedin.com/in/tsun-eugene-ting/)
