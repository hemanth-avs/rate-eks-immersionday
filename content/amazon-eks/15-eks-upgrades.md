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
* replace each of the nodegroups by creating a new one and deleting the old one
* update default add-ons (more about this here):
  * [Kube Proxy](https://docs.aws.amazon.com/eks/latest/userguide/managing-kube-proxy.html)
  * [VPC CNI - aws-node](https://docs.aws.amazon.com/eks/latest/userguide/managing-vpc-cni.html)
  * [Core DNS](https://docs.aws.amazon.com/eks/latest/userguide/managing-coredns.html)

### Listing enabled addons

```bash
eksctl get addons --cluster eksworkshop-eksctl
```

### Discovering addons

* You can discover what addons are available to install on your cluster by running:

```bash
eksctl utils describe-addon-versions --cluster eksworkshop-eksctl
```

* This will discover your cluster's kubernetes version and filter on that. Alternatively if you want to see what addons are available for a particular kubernetes version you can run:

```bash
eksctl utils describe-addon-versions --kubernetes-version 1.21
```

### Addon Update

```bash
eksctl utils update-kube-proxy --cluster=eksworkshop-eksctl --approve

eksctl utils update-coredns --cluster=eksworkshop-eksctl --approve

eksctl utils update-aws-node --cluster=eksworkshop-eksctl --approve
```
