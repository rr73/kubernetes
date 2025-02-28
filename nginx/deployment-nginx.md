This YAML configuration defines a Kubernetes setup with two Nginx deployments, each served by a separate service and exposed via an Ingress resource. Here's a breakdown of the configuration:

1. ConfigMaps
nginx-config1: Contains an index.html file with the content for the first Nginx deployment.

nginx-config2: Contains an index.html file with the content for the second Nginx deployment.

2. Deployments
nginx-deployment1:

Runs 3 replicas of the Nginx container.

Uses the nginx-config1 ConfigMap to serve the custom index.html file.

Resource limits and requests are set for CPU and memory.

nginx-deployment2:

Runs 3 replicas of the Nginx container.

Uses the nginx-config2 ConfigMap to serve the custom index.html file.

Resource limits and requests are set for CPU and memory.

3. Services
svc1:

A ClusterIP service that exposes nginx-deployment1 on port 80.

svc2:

A ClusterIP service that exposes nginx-deployment2 on port 80.

4. Ingress
nsx-l7-lb:

Routes traffic based on the host header:

Requests to lab-wm-1.c1cx.com are routed to svc1.

Requests to lab-wm-2.c1cx.com are routed to svc2.


Key Points:
ConfigMaps: Used to store the HTML content for each Nginx deployment.

Deployments: Ensure that 3 replicas of each Nginx application are running.

Services: Expose the deployments internally within the cluster.

Ingress: Routes external traffic to the appropriate service based on the hostname.

How It Works:
When a user accesses http://lab-wm-1.c1cx.com, the Ingress routes the request to svc1, which points to nginx-deployment1. The Nginx pod serves the index.html from nginx-config1.

When a user accesses http://lab-wm-2.c1cx.com, the Ingress routes the request to svc2, which points to nginx-deployment2. The Nginx pod serves the index.html from nginx-config2.

Notes:
Ensure that the DNS for lab-wm-1.c1cx.com and lab-wm-2.c1cx.com points to the Ingress controller's external IP.

The Ingress controller must be deployed in your cluster for this setup to work.

If you want to expose the services externally, you can change the type of the services to LoadBalancer or NodePort, depending on your environment.