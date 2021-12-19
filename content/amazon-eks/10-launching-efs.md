---
title: "EFS"
weight: 10
---

[EFS Lab](https://www.eksworkshop.com/beginner/190_efs/)

### EFS

![EFS](https://docs.aws.amazon.com/efs/latest/ug/images/efs-ec2-how-it-works-Regional.png)

### EFS Mount Targets

![Mount Targets](/images/efs-mount-target.png)

### EFS CSI driver

* Components
  * A controller that makes calls to EFS for dynamic provisioning
  * A node agent that mounts volumes into a pod - DS
* EC2
  * Requires CSI Driver
* Fargate
  * Dynamic provisioning is not yet supported for Fargate pods
  * With Amazon EKS on AWS Fargate, this node agent is built in
  * No need to deploy EDS CSI Driver
