# Ingress Demo

[Official Kubernetes Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)

## Ingress Overview

An Ingress manages external connections to Services within the Cluster, typically HTTPS(S). Exposing ports other than standard HTTP(S) ports is typically done with other Service types such as NodePort or LoadBalcner. Typical use cases include giving Services externally-reachable URLs, load balancing traffic, SSL termination, and offering name-based virtual hosting. 

To satisfy an Ingress, an Ingress controller is required. Only having an Ingress resource is not enough. A number of different types of Ingress controller exists, such as [ingress-nginx](https://kubernetes.github.io/ingress-nginx/deploy/). The ingress-nginx controller can be installed on the Docker Desktop Kubernetes server with:

    $ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.0/deploy/static/provider/cloud/deploy.yaml

Check that the controller started correctly by checking for pods in the `ingress-nginx` namespace:

    $ kubectl get pods --namespace=ingress-nginx 

## Ingress Resource

The `spec` field of the Ingress resource defines rules which controls how the traffic is routed. Each HTTP(S) rule contains the following information:

- Host (optional): Specifies the hosts to which this rule is applied. If left unspecified, the rule applies to all inbound traffic.
- A list of paths: Each path list must have an associated backend. Each path must also have a corresponding `pathType`.
- Backend: Typically references a Service, must be defined by both a `service.name` and `service.port.name` or `service.port.number`.

These fields specify how traffic is routed from the Ingress to other Services within the Cluster. A `defaultBackend` is used to determine how to route traffic which does not match any of the rules specified in the Ingress resource. Typically, this is a configuration option of the Ingress controller.

In addition to defining routing rules, Ingress frequently uses annotations to configure some options of the Ingress controller. Different controller provide different [supported annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/).

## Ingress Demo

### Resource Description

[pods.yaml](pods.yaml) contains the definition of two single container pods, both based off the official [httpd](https://hub.docker.com/_/httpd) base image. On start-up, `hello-pod` creates a new `/hello` directory in `htdocs` and writes into it an `index.html` file.

[service.yaml](service.yaml) contains the definition of two Service, each targetting one of the pods created.

[ingress.yaml](ingress.yaml) contains the defintion of a fanout Ingress, which requires a Nginx ingress controller to be running in the Cluster. In order for the controller to [discover](https://kubernetes.github.io/ingress-nginx/user-guide/basic-usage/) this Ingress resource, the `spec.ingressClassName` must be set to `nginx`. This Ingress routes traffic based on the HTTP URI. 

### Instructions

Create the required resources

    $ kubectl apply -f pods.yaml
    $ kubectl apply -f service.yaml
    $ kubectl apply -f ingress.yaml

By default, the Ingress will be bound to port 80 of `localhost`. However, if another service of type LoadBalancer is bound to port 80, or the port is unavailable for some other reason, you can forward another port on the local machine to port 80 of the Ingress.

    $ kubectl port-forward --namespace=ingress-nginx service/ingress-nginx-controller 8080:80

The Ingress, and therefore the Service backends specified by the Ingress resource, should now be reachable from outside the Cluster. Using the browser, visit http://localhost/. You should receive the default 'It works!' message of the `httpd` base image. Navigate to http://localhost/hello/, you should receive the custom 'Hello, Kubernetes!' message set by the `hello-pod` definition. 

This works because the Ingress was configured to route traffic whit URI `/` to the `my-svc` backend, which in turn targets the `root-pod`. If the Ingress receives a request with URI `/hello/`, the traffic is routed to the 'hello-svc' backend which targets the `hello-pod`. The `httpd` server receives a HTTP request for `/hello/` and serves the index.html page situated in the `/usr/local/apache2/htdocs/hello/` directory.

The created resources can bow be teared down using:

    $ kubectl delete -f pods.yaml
    $ kubectl delete -f service.yaml
    $ kubectl delete -f ingress.yaml