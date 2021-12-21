---
title: "LB IP & Instance Mode"
weight: 10
---

### IP Mode

Notice that the pods have been directly registered with the load balancer

While running under Fargate, ALB operates in IP Mode, where Ingress traffic starts at the ALB and reaches the Kubernetes pods directly.

Illustration of request routing from an AWS Application Load Balancer to Fargate Pods in IP mode:

![LoadBalancer IP Mode](https://www.eksworkshop.com/images/fargate/IPMode.png)


### Instance Mode

when using the worker nodes with Instance Mode, the IP address of the worker nodes and the NodePort will be registered as targets.

In this case, traffic starts at the ALB and reaches the Kubernetes worker nodes through each service’s NodePort and subsequently reaches the pods through the service’s ClusterIP.

Illustration of request routing from an AWS Application Load Balancer to Pods on worker nodes in Instance mode:

![LoadBalancer Instance Mode](https://www.eksworkshop.com/images/fargate/InstanceMode.png)

### AWS Load Balancer Controller documentation

https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.3/guide/ingress/annotations/
