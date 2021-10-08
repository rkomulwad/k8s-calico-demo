# k8s-calico-demo

Demonstrate how to configure Calico Network Policies to secure communication across the cluster

Goals of this project

1. Setup default calico network policies that disable all ingress, egress communication for the 2 namespaces sleep-ns & httpbin-ns
2. Setup explicit Network Policies that enable sleep container running in sleep-ns namespace to communicate with httpbin container running in the httpbin-ns namespace

## Setup Instructions

### start minikube with Calico CNI

```bash
minikube start --network-plugin=cni --cni=calico
```

### deploy httpbin

```bash
kubectl apply -f httpbin.yaml
```

### deploy sleep

```bash
kubectl apply -f sleep.yaml
```

## Apply Network Policies

### configure the default deny network policies

```bash
kubectl apply -f calico-global-deny.yaml
```

At this point the sleep app should not be able to communicate with httpbin. To verify run

```bash
kubectl -n sleep-ns exec deploy/sleep -- curl -si http://httpbin.httpbin-ns.svc.cluster.local:8000/status/200
```

### configure network policies that enable communication

```bash
kubectl apply -f calico-grant.yaml
```

Now the sleep app should be able to communicate with httpbin. To verify run

```bash
kubectl -n sleep-ns exec deploy/sleep -- curl -si http://httpbin.httpbin-ns.svc.cluster.local:8000/status/200
```

## Cleanup

```bash
kubectl delete -f calico-global-deny.yaml
kubectl delete -f calico-grant.yaml
kubectl delete -f httpbin.yaml
kubectl delete -f sleep.yaml
minikube delete --purge --all
```
