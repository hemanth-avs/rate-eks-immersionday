---
title: "RBAC"
weight: 13
---

[RBAC](https://www.eksworkshop.com/beginner/090_rbac/)

### Cluster authentication

![IAM](https://docs.aws.amazon.com/eks/latest/userguide/images/eks-iam.png)

### Get all identity mappings

```bash
eksctl get iamidentitymapping --cluster eksworkshop-eksctl

kubectl describe cm aws-auth -n kube-system
```

```yaml
Data
====
mapRoles:
----
- groups:
  - system:bootstrappers
  - system:nodes
  - system:node-proxier
  rolearn: arn:aws:iam::$ACCOUNT_ID:role/eksctl-eksworkshop-eksctl-FargatePodExecutionRole-J54ESQVOJ2GD
  username: system:node:{{SessionName}}
```

### Create Identity Mapping

```properties
eksctl create iamidentitymapping --cluster eksworkshop-eksctl --region=us-west-2 --arn arn:aws:iam::$ACCOUNT_ID:user/rbac-user --username rbac-user
```

```bash
kubectl describe cm -n kube-system aws-auth
```

```yaml
Data
====
mapRoles:
----
- groups:
  - system:bootstrappers
  - system:nodes
  - system:node-proxier
  rolearn: arn:aws:iam::$ACCOUNT_ID:role/eksctl-eksworkshop-eksctl-FargatePodExecutionRole-J54ESQVOJ2GD
  username: system:node:{{SessionName}}

mapUsers:
----
- userarn: arn:aws:iam::$ACCOUNT_ID:user/rbac-user
  username: rbac-user
```
