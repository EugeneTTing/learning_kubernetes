# Service Demo

[Official Kubernetes Documentation](https://kubernetes.io/docs/concepts/services-networking/service/) 

## Services Overview

Pods in Kubernetes are generally ephemeral. Pods are typically created by Deployemnts, which can create and destroy Pods dynamically to match the desired state of the cluster. Each time they are created, they may be assigned a different IP address. Therefore, it can become difficult to keep track of these IP addresses and connect to these applications.

Services can be used to solve this porblem. Services are used to expose network applications running on one or more Pods in a cluster. Each service targets and sends traffic to a logical set of endpoints, usually pods. Selectors can be used to determine which Pods are targetted. This allows loose coupling of different network application.

Several types of Services with different network visibility and functionality can be defined based on your needs. The default Service type is ClusterIP, which is only visisble within the cluster. This will be the type of Service used within this demonstration.

## Demonstration Instructions

Deploy the Kubernetes resources:

    $ kubectl apply -f busybox.yaml
    $ kubectl apply -f deployment.yaml
    $ kubectl apply -f service.yaml

The Deployment creates 3 Pods, each with one container running [this image](../docker/my_httpd/README.MD). The Service targets these Pods as endpoints using Selectors defined in the manifest. 

Verify these resources have been created:

    $ kubectl get pods
    $ kubectl get services

Open a shell terminal in the busybox pod:

    $ kubectl exec -it busybox -- sh

We use the `busybox` Pod to make a request since the Service of type ClusterIp is only visible from within the Cluster. From the `busybox` shell, issue the following command:

    # wget -qO- http://my-service.default/

This will send an HTTP request to the Service called `my-service`, in which is created in the `default` namespace. `my-service` the routes the traffic to the targetted endpoints, which in this case is the Pods created by the Deployment. The domain name could be resolved because of the CoreDNS add-on, which is a cluster aware DNS server running in the Cluster. It works by automatically creating new DNS entries when Services are created.

Running the above command multiple times in quick succession, you should get a different `"Hello world from [Pod name]!"` messages from each Pod. When the Deployment is created, a random string is assigned to each Pod to distinguish them. This shows that the Service is routing traffic to each of the Pods in the deployment.

Exit the shell session and clean-up the Kubernetes resources

    # exit
    $ kubectl delete -f deployment.yaml
    $ kubectl delete -f service.yaml
    $ kubectl delete -f busybox.yaml --grace-period=0 --force