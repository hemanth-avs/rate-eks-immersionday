---
title: "Autoscaling with HPA"
weight: 12
---

[Autoscaling with HPA and CA](https://www.eksworkshop.com/beginner/080_scaling/)


In this Chapter, we will show patterns for scaling your applications deployments automatically.

Automatic scaling in K8s comes in two forms:

* **Horizontal Pod Autoscaler (HPA)** scales the pods in a deployment or replica set. It is implemented as a K8s API resource and a controller. The controller manager queries the resource utilization against the metrics specified in each HorizontalPodAutoscaler definition. It obtains the metrics from either the resource metrics API (for per-pod resource metrics), or the custom metrics API (for all other metrics).

* **Cluster Autoscaler (CA)** a component that automatically adjusts the size of a Kubernetes Cluster so that all pods have a place to run and there are no unneeded nodes.

### Deploy the Metrics Server

Metrics Server is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines.

These metrics will drive the scaling behavior of the [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

We will deploy the metrics server using [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server).

```sh
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.5.0/components.yaml
```

Lets' verify the status of the metrics-server `APIService` (it could take a few minutes).

```sh
kubectl get apiservice v1beta1.metrics.k8s.io -o json | jq '.status'
```

```json
{
  "conditions": [
    {
      "lastTransitionTime": "2020-11-10T06:39:13Z",
      "message": "all checks passed",
      "reason": "Passed",
      "status": "True",
      "type": "Available"
    }
  ]
}
```

### Deploy a Sample App

We will deploy an application and expose as a service on TCP port 80.

The application is a custom-built image based on the php-apache image. The index.php page performs calculations to generate CPU load. More information can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#run-expose-php-apache-server)

```bash
kubectl create deployment php-apache --image=us.gcr.io/k8s-artifacts-prod/hpa-example
kubectl set resources deploy php-apache --requests=cpu=250m
kubectl expose deploy php-apache --port 80

kubectl get pod -l app=php-apache

kubectl -n default rollout status deployment php-apache
```

### Create an HPA resource

This HPA scales up when CPU exceeds 30% of the allocated container resource.

```bash
kubectl autoscale deployment php-apache `#The target average CPU utilization` \
    --cpu-percent=30 \
    --min=1 `#The lower limit for the number of pods that can be set by the autoscaler` \
    --max=10 `#The upper limit for the number of pods that can be set by the autoscaler`
```

View the HPA using kubectl. You probably will see `<unknown>/30%` for 1-2 minutes and then you should be able to see `0%/30%`

```bash
kubectl get hpa -w
```

### Generate load to trigger scaling

**Open a new terminal** in the Cloud9 Environment and run the following command to drop into a shell on a new container

```bash
kubectl run -i --tty load-generator --image=busybox /bin/sh
```

If Command timed out waiting for the condition, then exec to container with below command 

```bash
kubectl exec -it load-generator sh
```

Execute a while loop to continue getting http:///php-apache

```bash
while true; do wget -q -O - http://php-apache; done
```

In the previous tab, watch the HPA with the following command

```bash
kubectl get hpa -w
```

You will see HPA scale the pods from 1 up to our configured maximum (10) until the CPU average is below our target (30%)

```bash
TeamRole:~/environment/efs $ kubectl get hpa -w
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/30%    1         10        1          22s
php-apache   Deployment/php-apache   60%/30%   1         10        1          46s
php-apache   Deployment/php-apache   98%/30%   1         10        2          62s
php-apache   Deployment/php-apache   96%/30%   1         10        4          77s
php-apache   Deployment/php-apache   92%/30%   1         10        4          92s
php-apache   Deployment/php-apache   98%/30%   1         10        4          107s
php-apache   Deployment/php-apache   93%/30%   1         10        4          2m2s
php-apache   Deployment/php-apache   92%/30%   1         10        4          2m17s
php-apache   Deployment/php-apache   58%/30%   1         10        4          2m32s
php-apache   Deployment/php-apache   24%/30%   1         10        4          3m2s
```

You can now stop (_Ctrl + C_) load test that was running in the other terminal. You will notice that HPA will slowly bring the replica count to min number based on its configuration. You should also get out of load testing application by pressing _Ctrl + D_.

##### scale-down behavior

```bash
kubectl describe hpa php-apache
```

```properties
Name:                                                  php-apache
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Mon, 20 Dec 2021 22:57:28 +0000
Reference:                                             Deployment/php-apache
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  0% (1m) / 30%
Min replicas:                                          1
Max replicas:                                          10
Deployment pods:                                       1 current / 1 desired
Conditions:
  Type            Status  Reason            Message
  ----            ------  ------            -------
  AbleToScale     True    ReadyForNewScale  recommended size matches current size
  ScalingActive   True    ValidMetricFound  the HPA was able to successfully calculate a replica count from cpu resource utilization (percentage of request)
  ScalingLimited  True    TooFewReplicas    the desired replica count is less than the minimum replica count
Events:
  Type    Reason             Age   From                       Message
  ----    ------             ----  ----                       -------
  Normal  SuccessfulRescale  30m   horizontal-pod-autoscaler  New size: 4; reason: cpu resource utilization (percentage of request) above target
  Normal  SuccessfulRescale  22m   horizontal-pod-autoscaler  New size: 2; reason: All metrics below target
  Normal  SuccessfulRescale  21m   horizontal-pod-autoscaler  New size: 1; reason: All metrics below target
```

### Cleanup Resources

```bash

kubectl delete hpa,svc php-apache

kubectl delete deployment php-apache

kubectl delete pod load-generator

kubectl delete -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.4.1/components.yaml

```