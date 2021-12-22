---
title: "EKS Upgrades"
weight: 15
---

[EKS Upgrades](https://www.eksworkshop.com/intermediate/320_eks_upgrades/)

### AWS EKS Console

```bash
eksctl create iamidentitymapping --cluster eksworkshop-eksctl --arn arn:aws:iam::${ACCOUNT_ID}:role/TeamRole --group system:masters
```

### Cluster upgrades

* Upgrade control plane version with eksctl upgrade cluster
* update default add-ons (more about this here):
  * [Kube Proxy](https://docs.aws.amazon.com/eks/latest/userguide/managing-kube-proxy.html)
  * [VPC CNI - aws-node](https://docs.aws.amazon.com/eks/latest/userguide/managing-vpc-cni.html)
  * [Core DNS](https://docs.aws.amazon.com/eks/latest/userguide/managing-coredns.html)
* nodegroups upgrade
* Fargate
  * Any new pods launched on Fargate will have a kubelet version that matches your cluster version. Existing Fargate pods aren't changed.
  * kubectl delete pods --all --all-namespaces

### Upgrade EKS Control Plane

The first step of this process is to upgrade the EKS Control Plane.
Since we used eksctl to provision our cluster we’ll use that tool to do our upgrade as well.
First we’ll run this command

```properties
eksctl upgrade cluster --name=eksworkshop-eksctl
```

You’ll see in the output that it found our cluster, worked out that it is 1.20 and the next version is 1.21 (you can only go to the next version with EKS) and that everything is ready for us to proceed with an upgrade.

```bash
$ eksctl upgrade cluster --name=eksworkshop-eksctl
[ℹ]  eksctl version 0.66.0
[ℹ]  using region us-west-2
[ℹ]  (plan) would upgrade cluster "eksworkshop-eksctl" control plane from current version "1.20" to "1.21"
[ℹ]  re-building cluster stack "eksctl-eksworkshop-eksctl-cluster"
[✔]  all resources in cluster stack "eksctl-eksworkshop-eksctl-cluster" are up-to-date
[ℹ]  checking security group configuration for all nodegroups
[ℹ]  all nodegroups have up-to-date configuration
[!]  no changes were applied, run again with '--approve' to apply the changes
```

We’ll run it again with an –approve appended to proceed

```bash
eksctl upgrade cluster --name=eksworkshop-eksctl --approve
```

### Listing enabled addons

```bash
eksctl get addons --cluster eksworkshop-eksctl
```

### Upgrade EKS ADD-ONS

```bash
eksctl utils update-kube-proxy --cluster=eksworkshop-eksctl --approve

eksctl utils update-coredns --cluster=eksworkshop-eksctl --approve

eksctl utils update-aws-node --cluster=eksworkshop-eksctl --approve
```

##### Discovering addons

* You can discover what addons are available to install on your cluster by running:

```bash
eksctl utils describe-addon-versions --cluster eksworkshop-eksctl
```

* This will discover your cluster's kubernetes version and filter on that. Alternatively if you want to see what addons are available for a particular kubernetes version you can run:

```bash
eksctl utils describe-addon-versions --kubernetes-version 1.21
```
