---
title: "Expose Application through LB"
weight: 7
draft: false
---

Before we bring up the frontend service, let's take a look at the service types
we are using:
This is `files/yelb/yelb-k8s-loadbalancer.yaml` for our frontend service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: yelb-ui
  namespace: fargate
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "external"
    service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: "ip"
    service.beta.kubernetes.io/aws-load-balancer-scheme: internet-facing
  labels:
    app: yelb-ui
    tier: frontend
spec:
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: yelb-ui
    tier: frontend
```

Notice `type: LoadBalancer`: This will provision and configure a Network Load Balancers to handle incoming traffic to this service.

Compare this to `files/yelb/yelb-k8s-manifest.yaml` for one of our backend services:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: yelb-appserver
  namespace: fargate
  labels:
    app: yelb-appserver
    tier: middletier
spec:
  type: ClusterIP
  ports:
  - port: 4567
  selector:
    app: yelb-appserver
    tier: middletier
```

Notice there is no specific service type described. When we check [the kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)
we find that the default type is `ClusterIP`. This Exposes the service on a cluster-internal IP.
Choosing this value makes the service only reachable from within the cluster.

### Deploy LB Service

```properties
kubectl apply -f ./files/yelb/yelb-k8s-loadbalancer.yaml
```

Now that we have a running service that is `type: LoadBalancer` we need to find
the ELB's address.  We can do this by using the `get services` operation of kubectl:

```properties
kubectl get service -n fargate
```

If we wanted to use the data programatically, we can also output via json. This is
an example of how we might be able to make use of json output:

```bash
ELB=$(kubectl get service yelb-ui -n fargate -o json | jq -r '.status.loadBalancer.ingress[].hostname')

curl -m3 -v $ELB
```

{{% notice tip %}}
It will take several minutes for the ELB to become healthy and start passing traffic to the frontend pods.
{{% /notice %}}

You should also be able to copy/paste the loadBalancer hostname into your browser and see the application running.

### AWS Console

View LB Registered targets by browsing to https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#LoadBalancers

Select LB -> Listeners Tab -> Click on the Target Group Link from the Table

from **Target Group** link, you will see the IP addresses and ports of the yelb app pods listed.

![NLB-Registered-targets.png](/images/lb/NLB-Registered-targets.png)
