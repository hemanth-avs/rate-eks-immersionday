---
title: "Fargate"
weight: 6
draft: false
---

### Fargate pod configuration

|   vCPU value      |   Memory value                                |
|   ---             |   ---                                         |
|   .25 vCPU        |   0.5 GB, 1 GB, 2 GB                          |
|   .5 vCPU         |   1 GB, 2 GB, 3 GB, 4 GB                      |
|   1 vCPU          |   2 GB, 3 GB, 4 GB, 5 GB, 6 GB, 7 GB, 8 GB    |
|   2 vCPU          |   Between 4 GB and 16 GB in 1-GB increments   |
|   4 vCPU          |   Between 8 GB and 30 GB in 1-GB increments   |

* When pods are scheduled on Fargate, the vCPU and memory reservations within the pod specification determine how much CPU and memory to provision for the pod.
* Fargate adds 256 MB to each pod's memory reservation for the required Kubernetes components (kubelet, kube-proxy, and containerd).
* The CapacityProvisioned annotation represents the enforced pod capacity and it determines the cost of your pod running on Fargat
* [Doc - Fargate pod configuration](https://docs.aws.amazon.com/eks/latest/userguide/fargate-pod-configuration.html)

| Requested         | Provisioned                                   |
| ---               | ---                                           |
| 250m cpu, 1000Mi  |  0.25 vCPU 2GB                                |
| 255m cpu, 1000Mi  |  0.50 vCPU 2GB                                |
| 260m cpu, 1750Mi  |  0.50 vCPU 2GB                                |
| 255m cpu, 1800Mi  |  0.50 vCPU 3GB                                |
| 255m cpu, 10Gi    |  2.00 vCPU 11GB                               |
|    9 cpu, 10Gi    |  Your pod's cpu/memory requirements exceed the max Fargate configuration|
| 255m cpu, 31Gi    |  Your pod's cpu/memory requirements exceed the max Fargate configuration|
