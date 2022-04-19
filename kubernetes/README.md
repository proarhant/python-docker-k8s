
For this demo purpose, I will deploy this Dockerized web app in `Kubernetes` using `LoadBalancer` service in a `minikube` K8s cluster. In production environment, we mostly use `Ingress` that exposes HTTP and HTTPS routes from outside the K8s cluster to services within the cluster.
 
In a `minikube` cluster, the following resources will be deployed for this sample stateless Python Flask application.

![image](https://user-images.githubusercontent.com/2681229/163902644-4ab1496e-82cf-4780-b7c4-43947a4a84f6.png)
