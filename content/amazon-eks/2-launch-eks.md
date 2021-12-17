---
title: "Launch EKS"
chapter: false
weight: 2
---

In this module, we will use eksctl to launch and configure our EKS cluster and nodes.

#### EKS Config file

```yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eksfargate
  region: us-west-2
  version: "1.20"

availabilityZones: ["us-west-2a", "us-west-2b", "us-west-2c"]

fargateProfiles:
  - name: fp-fargate
    selectors:
      - namespace: kube-system
      - namespace: fargate

managedNodeGroups:
- name: nodegroup
  desiredCapacity: 3

# To enable all of the control plane logs, uncomment below:
cloudWatch:
  clusterLogging:
    enableTypes: ["*"]
```

### Create EKS Cluster

{{% notice info %}}
We are deliberatly launching at least one Kubernetes version behind the latest available on [Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/kubernetes-versions.html). This allows you to perform the [cluster upgrade](https://www.eksworkshop.com/intermediate/320_eks_upgrades/) lab.
{{% /notice %}}

```bash
eksctl create cluster \
    -f ~/rate-eks-immersionday/files/eksfargate.yaml
```

{{% notice info %}}
Launching EKS and all the dependencies will take approximately 15 minutes
{{% /notice %}}
