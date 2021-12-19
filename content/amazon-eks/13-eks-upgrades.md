---
title: "EKS Upgrades"
weight: 13
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
