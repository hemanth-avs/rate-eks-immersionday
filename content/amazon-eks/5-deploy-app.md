---
title: "Deploy Yelb Application"
weight: 5
draft: false
---

### Yelb architecture

![Image](https://raw.githubusercontent.com/mreferre/yelb/master/images/yelb-architecture.png)

### Deploy Yelb Application

Copy/Paste the following commands into your Cloud9 workspace:

```properties
cd ~/rate-eks-immersionday
kubectl apply -f ./files/yelb-k8s-manifest.yaml
```

```properties
namespace/fargate created
service/redis-server created
service/yelb-db created
service/yelb-appserver created
deployment.apps/yelb-ui created
deployment.apps/redis-server created
deployment.apps/yelb-db created
deployment.apps/yelb-appserver created
```

We can watch the progress by looking at the deployment status:

```properties
kubectl get deployment -n fargate
```

```properties
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
redis-server     1/1     1            1           1m15s
yelb-appserver   1/1     1            1           1m15s
yelb-db          1/1     1            1           1m15s
yelb-ui          3/3     3            3           1m15s
```

### Fargate Nodes

Next, run the following command to list all the nodes in the EKS cluster and you should see output as follows:

```properties
kubectl get pods -A
kubectl get nodes -A
```

```properties
NAMESPACE     NAME                                            READY   STATUS    RESTARTS   AGE
fargate       redis-server-74556bbcb7-gwpll                   1/1     Running   0          54m
fargate       yelb-appserver-d584bb889-65xjv                  1/1     Running   0          54m
fargate       yelb-db-694586cd78-gd7bt                        1/1     Running   0          54m
fargate       yelb-ui-798667d648-fmcdp                        1/1     Running   0          54m
fargate       yelb-ui-798667d648-h2j29                        1/1     Running   0          54m
fargate       yelb-ui-798667d648-sdq8z                        1/1     Running   0          54m
kube-system   aws-load-balancer-controller-654bcfd665-bm5bz   1/1     Running   0          20h
kube-system   aws-load-balancer-controller-654bcfd665-cd2hp   1/1     Running   0          20h
kube-system   coredns-867ff8777b-67v8d                        1/1     Running   0          24h
kube-system   coredns-867ff8777b-l2lkz                        1/1     Running   0          24h
```

```properties
NAME                                                    STATUS   ROLES    AGE   VERSION
fargate-ip-192-168-112-0.us-west-2.compute.internal     Ready    <none>   54m   v1.20.7-eks-135321
fargate-ip-192-168-124-122.us-west-2.compute.internal   Ready    <none>   20h   v1.20.7-eks-135321
fargate-ip-192-168-125-158.us-west-2.compute.internal   Ready    <none>   24h   v1.20.7-eks-135321
fargate-ip-192-168-128-75.us-west-2.compute.internal    Ready    <none>   54m   v1.20.7-eks-135321
fargate-ip-192-168-144-254.us-west-2.compute.internal   Ready    <none>   54m   v1.20.7-eks-135321
fargate-ip-192-168-147-49.us-west-2.compute.internal    Ready    <none>   54m   v1.20.7-eks-135321
fargate-ip-192-168-154-96.us-west-2.compute.internal    Ready    <none>   24h   v1.20.7-eks-135321
fargate-ip-192-168-162-106.us-west-2.compute.internal   Ready    <none>   53m   v1.20.7-eks-135321
fargate-ip-192-168-181-187.us-west-2.compute.internal   Ready    <none>   20h   v1.20.7-eks-135321
fargate-ip-192-168-188-73.us-west-2.compute.internal    Ready    <none>   54m   v1.20.7-eks-135321
```

If your cluster has any worker nodes, they will be listed with a name starting wit the **ip-** prefix.

In addition to the worker nodes, if any, there will now be five additional **fargate-** nodes listed. These are merely kubelets from the microVMs in which your sample app pods are running under Fargate, posing as nodes to the EKS Control Plane. This is how the EKS Control Plane stays aware of the Fargate infrastructure under which the pods it orchestrates are running. There will be a “fargate” node added to the cluster for each pod deployed on Fargate.