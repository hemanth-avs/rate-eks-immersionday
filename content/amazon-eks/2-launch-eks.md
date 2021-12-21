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
  name: eksworkshop-eksctl
  region: us-west-2
  version: "1.20"

availabilityZones: ["us-west-2a", "us-west-2b", "us-west-2c"]

fargateProfiles:
  - name: fp-fargate1
    selectors:
      - namespace: kube-system
      - namespace: default
      - namespace: fargate
      - namespace: storage
      - namespace: rbac-test
  - name: fp-fargate2
    selectors:
      - namespace: development
      - namespace: integration
      - namespace: gatekeeper-system
      - namespace: amazon-cloudwatch

#managedNodeGroups:
#- name: nodegroup
#  desiredCapacity: 3

# To enable all of the control plane logs, uncomment below:
cloudWatch:
  clusterLogging:
    enableTypes: ["*"]

iam:
  withOIDC: true

addons:
- name: vpc-cni
  version: latest
  attachPolicyARNs:
  - arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy
- name: kube-proxy
  version: latest
- name: coredns
  version: latest
```

### VPC CNI

* EKS supports native VPC networking via the VPC CNI plugin for Kubernetes
* Pod is a 1st class citizen in the VPC and gets an IP address picked from the VPC CIDR block
* Pod’s IP address is routable from anywhere within the VPC

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

### Fargate Profile

```bash
eksctl get fargateprofile --cluster eksworkshop-eksctl --name fp-fargate2 -o yaml
```

```yaml
- name: fp-fargate2
  podExecutionRoleARN: arn:aws:iam::account-id:role/eksctl-eksworkshop-eksctl-FargatePodExecutionRole-UAVYAQ9MBU54
  selectors:
  - namespace: development
  - namespace: integration
  - namespace: gatekeeper-system
  - namespace: amazon-cloudwatch
  status: ACTIVE
  subnets:
  - subnet-0d2dd9aa4bf87af50
  - subnet-051fe05f945d42dc0
  - subnet-0313b312b38ba641c
```
