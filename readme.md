![Project map](project.png)

# client image

This image is a react project for presentation layer

# server image

this image contains the api request

# worker image

redis image for calculating the fib value inserted in the presentation layer

# postgres image

postgres database to store values

## Installation

### client image

```bash
docker build -t <dockerhub_username>/multi-client ./client
docker login
docker push <dockerhub_username>/multi-client
```

### server image

```bash
docker build -t <dockerhub_username>/multi-server ./server
docker login
docker push <dockerhub_username>/multi-server
```

### worker image

```bash
docker build -t <dockerhub_username>/multi-worker ./worker
docker login
docker push <dockerhub_username>/multi-worker
```

### network policy

```bash
minikube start  --driver=hyperv  --network-plugin=cni --cni=calico
minikube addons  enable ingress
kubectl describe service -n ingress-nginx ingress-nginx-controller
```

### apply k8s to cluster

```bash
kubectl create secret generic pgpassword --from-literal PGPASSWORD=password123
kubectl apply -f ./k8s
```


## argocd installation 
```bash
kubectl create namespace argocd
kubectl apply -f ./argocd/install/install.yaml
# or install is using 
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
brew install argocd
kubectl port-forward svc/argocd-server -n argocd 8080:443 # http://localhost:8080
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
export ARGOCD_OPTS='--port-forward-namespace argocd'
argocd login localhost:8080
# Change the password using the command:
argocd account update-password
argocd cluster list
argocd cluster add docker-desktop
# output 
# https://kubernetes.default.svc  in-cluster           Unknown  Cluster has no applications and is not being monitored.
kubectl get deploy -n argocd -o=yaml
argocd proj create default
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
kubectl port-forward svc/guestbook-ui -n default  8081:80
argocd app get guestbook

```