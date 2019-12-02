# How to run it localy
## Deploy the minikube cluster with PSP

This commands should be run into this git repo.

```
K8SADDONS=~/.minikube/files/etc/kubernetes/addons/
K8SPSP=./psp.yaml

mkdir -p $K8SADDONS
cp $K8SPSP $K8SADDONS

minikube start \
  --kubernetes-version=v1.12.0 \
  --cpus 4 \
  --memory 4092 \
  --extra-config=apiserver.enable-admission-plugins=PodSecurityPolicy \
  --alsologtostderr -v=1
```
## Deploying the Helm Charts
Be carefull, I am using `helm v3` and as I still have `helm v2` I rename it `helm3`... adapt it to your environment ;-).

```
helm3 repo add stable https://kubernetes-charts.storage.googleapis.com/
helm3 repo add jupyterhub https://jupyterhub.github.io/helm-chart/
helm3 repo update

RELEASE=jhub-app
NAMESPACE=svc-jhub

kubectl create ns $NAMESPACE &&\
helm3 upgrade --install $RELEASE jupyterhub/jupyterhub \
  --namespace $NAMESPACE  \
  --version=0.8.2 \
  --values hub.yaml \
  --debug
```

## Manually fix the deployment
During the helm deployment (before it failed !)

- Add the `securityContext` to the 3 containers in the `hook-image-puller` ds:
```
securityContext:
            runAsUser: 1000
```

- Delete the `hook-image-awaiter` job:
```
kubectl delete -n svc-jhub job hook-image-awaiter
```

- Add the `securityContext` to the containers inside the 2 new deployments `hub` and `proxy`.
```
securityContext:
            runAsUser: 1000
```

## Access to JHub

Get the fake LB IP and connect
```
kubectl --namespace=svc-jhub get svc proxy-public
```

# Commands to remember:

```
minikube dashboard
kubectl get svc traefik-ingress --namespace kube-system -w
minikube tunnel
```

Removing properly the chart:
```
helm3 uninstall $RELEASE --debug && \
kubectl delete -n $NAMESPACE daemonset hook-image-puller && \
kubectl delete -n $NAMESPACE job hook-image-awaiter
```
