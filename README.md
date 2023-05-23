# Run Stock Ticker Web Service on Minikube & Docker
## Overview

The StockTicker  REST API service will get the last "n" days of a stocks closing price data along with the average closing price over those "n" days. 

URL: http://127.0.0.1:5000/stockticker?symbol=TSLA&ndays=40

## Pre-requisites 
Install below softwares in your laptop. The below command are tested on Mac
```
Docker Desktop
Minikube - brew install kubectl (Driver is Docker by default)
Kubectl - brew install kubectl
```

## Run the code in Minikube
Start minikube 
```
minikube start
minikube addons enable metrics-server  # for Dashboard
minikube addons enable ingress   # Enable ingress addon
minikube dashboard  # Open the dashboard
```

Store your Alpha Adavantage Api Key as K8s secret object in the K8s cluster. This will be accessed by web service python code as environment variable with in the container
```
kubectl create secret generic stockticker-api-key --from-literal=ALPHA_API_KEY='<ALPHA_API_KEY>'
```

Create Deployment, Service and Ingress objects. 

```
kubectl create -f manifest.yml
```

Once the pod is running, the API is accessible within the cluster only. One quick way to verify the deployment from our localhost is by doing port forwarding. 
```
kubectl port-forward <pod name> 5000:5000
curl http://127.0.0.1:5000/stockticker?symbol=TSLA&ndays=40
```

To manage external access to the services in a cluster, we need to use ingress. Close the port-forwarding. To get ingress to work on Docker Desktop, open a new terminal window and run below command
```
minikube tunnel
```

Check that everything is created and the pod is running
```
kubectl get deploy -A
kubectl get svc -A
kubectl get pods -A
kubectl get ing -A
```

Access the REST API using the below link
```
http://127.0.0.1/stockticker?symbol=TSLA&ndays=3
```

## Cleanup
Delete all of the minikube clusters
```
minikube delete --all
```