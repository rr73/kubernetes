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






Traffic Flow
Client Request:

A client makes a request to your application (e.g., http://lab-wm-1.c1cx.com or https://lab-wm-1.c1cx.com).

Kemp Load Balancer:

The Kemp Load Balancer intercepts the traffic.

If the request is on HTTP (port 80), the Kemp Load Balancer redirects it to HTTPS (port 443).

If the request is already on HTTPS (port 443), the Kemp Load Balancer forwards it to the Kubernetes Ingress.

Kubernetes Ingress:

The Ingress receives the HTTPS traffic (port 443) from the Kemp Load Balancer.

The Ingress terminates the TLS connection (decrypts the traffic) using the TLS secret.

The decrypted HTTP traffic is forwarded to the appropriate backend service (svc1 or svc2) on port 80.

Backend Services:

The services (svc1 and svc2) receive the HTTP traffic on port 80 and route it to the Nginx pods.




Since the Kemp Load Balancer is already handling the HTTP-to-HTTPS redirection, you only need to ensure that:

The Ingress is configured for TLS:

The Ingress should have the tls section to handle HTTPS traffic.

Example: yaml


tls:
- hosts:
  - lab-wm-1.c1cx.com
  - lab-wm-2.c1cx.com
  secretName: tls-secret

  
The TLS Secret Exists:

Ensure the TLS secret (tls-secret) is created in your Kubernetes cluster and contains the SSL certificate and private key.

Example command to create the secret:

kubectl create secret tls tls-secret --cert=path/to/tls.crt --key=path/to/tls.key

No HTTP-to-HTTPS Redirect in the Ingress:

Since the Kemp Load Balancer is handling the redirection, you do not need to add the nginx.ingress.kubernetes.io/ssl-redirect: "true" annotation to the Ingress.