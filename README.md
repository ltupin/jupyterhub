# How ro run it localy

Following this [link](https://github.com/kubernetes/minikube/issues/3818
), let's activate PSP on a minikube cluster. If you want to check (you have to) the PSP, follow this [link](https://gist.githubusercontent.com/ltupin/0c9205777b2bba167e763275b38b25aa/raw/b2238e901ff22a4ae526df81817ce28bc17b641d/psp.yaml).

```
K8SADDONS=~/.minikube/files/etc/kubernetes/addons/
K8SPSP=https://gist.githubusercontent.com/ltupin/0c9205777b2bba167e763275b38b25aa/raw/b2238e901ff22a4ae526df81817ce28bc17b641d/psp.yaml

mkdir -p $K8SADDONS
cd $K8SADDONS && { curl -LO $K8SPSP ; cd -; }

minikube start \
  --kubernetes-version=v1.12.0 \
  --cpus 4 \
  --memory 4092 \
  --extra-config=apiserver.enable-admission-plugins=PodSecurityPolicy \
  --alsologtostderr -v=1
```

Be carefull, I am using helm version 3 and as I still have version 2 I rename it helm3, adapt it to your environment ;-).

```
helm3 repo add stable https://kubernetes-charts.storage.googleapis.com/
helm3 repo add jupyterhub https://jupyterhub.github.io/helm-chart/
helm3 repo update
```

Deploy the chart:

```
RELEASE=jhub-app
NAMESPACE=svc-jhub

kubectl create ns $NAMESPACE &&\
helm3 upgrade --install $RELEASE jupyterhub/jupyterhub \
  --namespace $NAMESPACE  \
  --version=0.8.2 \
  --values hub.yaml \
  --debug
```

Remove properly the chart:

```
helm3 uninstall $RELEASE --debug && \
kubectl delete -n $NAMESPACE daemonset hook-image-puller && \
kubectl delete -n $NAMESPACE job hook-image-awaiter
```

Commands to remember:

```
minikube dashboard
kubectl get svc traefik-ingress --namespace kube-system -w
minikube tunnel
```
