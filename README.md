
# Simple Web-App for IKS Demo

This Webserver based on NGINX that serves a simple page containing its hostname, IP address and port as well as the requested URI and the local time of the webserver.

Created for various Intersight Kubernetes Service demos

### Steps

1. Create the deployment. This will create one pod which will get an IP from the POD IP CIDR
`kubectl create deployment acme --image=ghcr.io/pl247/web-app:latest --port=8888`

2. Create the service to expose the deployment
`kubectl expose deployment acme --type=ClusterIP --port 80`

3. Modify the ingress service to enable the /acme URI for the backend by creating a file called `acme-ingress.yaml` and put in it

```kind: Ingress
apiVersion: extensions/v1beta1
metadata:
  name: acme-ingress
  namespace: default
  
spec:
  rules:
    - http:
        paths:
          - path: /acme
            backend:
              serviceName: acme
              servicePort: 80
```

Then apply the changes to the ingress:
`kubectl apply -f acme-ingress.yaml`

4. Determine the ingress IP that MetalLB is using
`kubectl get services -A` 
and go to that URL http://<IP Address of ingress>/acme

5. Scale the deployment to 10 webservers
`kubectl scale deployment acme --replicas=10`

6. Check the newly deployed webservers
`kubectl get pods -o wide`


### Troubleshooting Commands

Get the deployment
`kubectl get deployments -o wide`

Get the pods
`kubectl get pods`

Describe the pods
`kubectl describe pods`

Get/describe the services
`kubectl get services acme`
`kubectl describe services acme`

Get/describe the ingress
`kubectl get ingress`
`kubectl describe ingress`

