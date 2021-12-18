---
title: "Ingress"
weight: 7
---

AWS Load Balancer Controller create Application Load Balancer (ALB) when you create a Kubernetes Ingress Object

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: "yelb-ui"
  namespace: fargate
  annotations:
    kubernetes.io/ingress.class: alb # check this, your ingress.class may be different 
    alb.ingress.kubernetes.io/scheme: internet-facing 
    alb.ingress.kubernetes.io/target-type: ip 
  labels:
    app: "yelb-ui"
spec:
  rules:
    - http:
        paths:
          - path: /*
            backend:
              serviceName: "yelb-ui"
              servicePort: 80
```

Expose Yelb application through Ingress

```properties
kubectl apply -f ./files/yelb/yelb-k8s-ingress-alb-ip.yaml -n fargate
```

```properties
kubectl describe ingress yelb-ui -n fargate
```

After few seconds, verify that the Ingress resource is enabled:

You should be able to see the following output

```properties
Name:             yelb-ui
Namespace:        fargate
Address:          k8s-fargate-yelbui-027f8166b6-1241061016.us-west-2.elb.amazonaws.com
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /*   yelb-ui:80 (192.168.112.0:80,192.168.144.254:80,192.168.147.49:80)
Annotations:  alb.ingress.kubernetes.io/scheme: internet-facing
              alb.ingress.kubernetes.io/target-type: ip
              kubernetes.io/ingress.class: alb
Events:
  Type    Reason                  Age   From     Message
  ----    ------                  ----  ----     -------
  Normal  SuccessfullyReconciled   3m   ingress  Successfully reconciled
```

{{% notice warning %}}
It could take 2 or 3 minutes for the ALB to be ready.
{{% /notice %}}

From your AWS Management Console, if you navigate to the EC2 dashboard and the select **[Load Balancers](https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#LoadBalancers)** from the menu on the left-pane, you should see the details of the ALB.

Select ALB -> Listeners Tab -> Click on the Rules Link from the Table
![LoadBalancer Dashboard](/images/lb/rules.png)

Click the **Target Group** link, you will see the IP addresses and ports of the yelb app pods listed.
![LoadBalancer Targets](/images/lb/NLB-Registered-targets.png)

At this point, your deployment is complete and you should be able to reach the yelb service from a browser using the DNS name of the ALB. You may get the DNS name of the load balancer either from the AWS Management Console or from the output of the following command.

```bash
export FARGATE_YELB_URL=$(kubectl get ingress/yelb-ui -n fargate -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

echo "http://${FARGATE_YELB_URL}"
```

Output should look like this

```properties
http://k8s-fargate-yelbui-027f8166b6-1241061016.us-west-2.elb.amazonaws.com
```
