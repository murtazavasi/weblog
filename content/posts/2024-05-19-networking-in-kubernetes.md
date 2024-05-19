+++
title = 'Networking in Kubernetes'
date = 2024-05-19T14:27:32+05:30
draft = true
slug = 'networking-in-kubernetes'
tags = [
  "networking",
  "nginx-ingress-controller",
  "node port service",
  "load balancer service",
  "cluster ip service"
]

+++

In kubernetes, when we deploy an application, our end goal is for the end customer to be able to access that application in some way or form though the internet. Since the kubernetes clusters can get pretty complex, pretty fast sometimes it might be difficult to understand why a particular issue has risen. To deepen the knowledge on Networking in Kubernetes, in today’s blog post lets go over the end to end flow from how a request is generated in the client’s browser and how it reaches it’s the application i.e. the pod running on the cluster.

## Exposing an application to the Internet

An application can be exposed to the internet by using couple of methods which we will briefly talk about here:

1. Via NodePort Service
2. Via Load Balancer Service
3. Via an Ingress Controller

Lets talk about each method in brief

**Via NodePort Service:** An application can be deployed and exposed to the internet via a NodePort Service in kubernetes which will expose the application at a particular port on that node.

![NodePort Service.png](/images/NodePort_Service.png)

For instance if the pod, housing the application is running on a node with IP 10.1.1.1 and 10.1.1.2 then if we create a nodeport service for the application then that application can be accessed using the `IP:Port` where IP is 10.1.1.1 or 10.1.1.2 and Port is the NodePort that is exposed (It will be between 30000 to 32767).

**Via Load Balancer Service**: Using this method we just need to create a load balancer type of service for our application and if we have a cluster running in the cloud then a load balancer will be automatically created in it with a public ip via which we can access the application

![Load Balancer Service.png](/images/Load_Balancer_Service.png)

**Via Ingress Controller**: This method requires a bit of setup but it has its advantages and in industries this is the preferred method to access an application. One of the main advantages of using an Ingress Controller over using Load Balancer Service is that for larger clusters that use more number of load balancers services the number of load balancers will increase which will drive up the cost in a cloud environment. For exposing an application using an ingress controller we can use any ingress controller the most used nginx is an option to try with. Once that is deployed we can use an ingress resource to expose our application to the internet.

![Ingress Controller.png](/images/Ingress_Controller.png)

## Assumption

For understanding the end to end flow of how a request reaches an application deployed in a cluster, let’s first assume a couple of things to help us in understanding the flow. First the application is deployed in a kubernetes cluster deployed in cloud and is exposed to the internet using an Ingress resource with nginx as our Ingress Controller. The domain that the user needs to use for accessing the application is `test.example.com`

## Flow of a Request

When a client hits `[test.example.com](http://test.example.com)` in the browser then first and foremost DNS resolution happens for the domain which is a fancy way of saying that the domain gets resolved to an IP address. This IP that we get (in our case) is for the Ingress Controller’s Load Balancer this request is then forwarded to the Nginx Pod (that is the backend service for the ingress controller) this pod will basically perform a check to see where it needs to forward the incoming request. Once the nginx pod finds the pod it needs to forward the traffic to then the request is forwarded to the application backend.

![Request Flow.png](/images/Request_Flow.png)

## Conclusion

In conclusion, understanding how networking works in Kubernetes is crucial for managing applications effectively. Knowing the journey of a request from the client's browser to the application pod running on the cluster can help troubleshoot issues and optimize performance. Whether you choose to expose your application through NodePort Service, Load Balancer Service, or an Ingress Controller, each method has its own advantages and suitable use cases. By leveraging these insights, you can ensure that your applications are accessible and running efficiently, providing a seamless experience for your end-users.

## References

- [https://medium.com/@seanlinsanity/how-to-expose-applications-running-in-kubernetes-cluster-to-public-access-65c2fa959a3b](https://medium.com/@seanlinsanity/how-to-expose-applications-running-in-kubernetes-cluster-to-public-access-65c2fa959a3b#:~:text=In%20Kubernetes%2C%20a%20NodePort%20service,is%20known%20as%20the%20NodePort)
- [https://betterprogramming.pub/north-south-communication-in-kubernetes-how-does-a-client-talk-to-a-service-inside-a-cluster-8af8b27dbb9](https://betterprogramming.pub/north-south-communication-in-kubernetes-how-does-a-client-talk-to-a-service-inside-a-cluster-8af8b27dbb9)