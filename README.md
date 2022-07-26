
# Simple Web-App for an Intersight Kubernetes Service Demo

This app is a simple, small containerized NGINX webserver that serves a simple page containing pod IP, service_port, pod_name as, date/time and URI.

Created for various Intersight Kubernetes Service demos.

## Steps to run this Web-App on Kubernetes

1. Create the deployment. This will create one pod which will get an IP from the POD IP CIDR

```
kubectl create deployment acme --image=ghcr.io/pl247/web-app:latest --port=8888
```

2. Create the service to expose the deployment on port 80

```
kubectl expose deployment acme --type=ClusterIP --port 80
```

3. Modify the ingress service to enable the /acme URI for the backend by creating a file called `acme-ingress.yaml` and put in it:

```
kind: Ingress
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

Then apply the changes to the ingress pointing /acme to the acme service:

```
kubectl apply -f acme-ingress.yaml
```

4. Determine the ingress_IP that MetalLB is using

```
kubectl get services -A
``` 

and go to the URL http://ingress_IP/acme

5. Scale the deployment to 10 webservers

```
kubectl scale deployment acme --replicas=10
```

6. Check the newly deployed webservers

```
kubectl get pods -o wide
```


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


## Notes

The creation of this container app is fully automated using the GitHub Actions CI/CD pipeline that completely automates the build of the container as well as the publishing of it on the GitHub Container Registry at ```
ghcr.io/pl247/webapp:latest
```


