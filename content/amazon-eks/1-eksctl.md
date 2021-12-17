---
title: "EKSCTL"
chapter: false
weight: 1
---

> [eksctl](https://eksctl.io) is a tool jointly developed by AWS and [Weaveworks](https://weave.works) that automates much of the experience of creating EKS clusters.

#### Simple Cluster

```yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: cluster-name
  region: us-west-2
  version: "1.21"

availabilityZones: ["us-west-2a", "us-west-2b", "us-west-2c"]

managedNodeGroups:
- name: ondemand-ng
  desiredCapacity: 3
  minSize: 3
  maxSize: 5
  instanceType: t3.small

# Default cidr: 192.168.0.0/16

```

#### Fargate Profiles

```yaml
fargateProfiles:
  - name: fp-default
    selectors:
      # All workloads in the "default" Kubernetes namespace will be
      # scheduled onto Fargate:
      - namespace: default
      # All workloads in the "kube-system" Kubernetes namespace will be
      # scheduled onto Fargate:
      - namespace: kube-system
  - name: fp-dev
    selectors:
      # All workloads in the "dev" Kubernetes namespace matching the following
      # label selectors will be scheduled onto Fargate:
      - namespace: dev
        labels:
          env: dev
          checks: passed
```

#### Spot

```yaml
managedNodeGroups:
- name: spot-ng
  instanceTypes: 
  - c5.large
  - c5a.large
  - t3.medium
  - t3a.medium
  spot: true
```

#### Spot with mixed instances distribution

```yaml
nodeGroups:
- name: ng-1
  minSize: 2
  maxSize: 5
  instancesDistribution:
    maxPrice: 0.017
    instanceTypes: ["t3.small", "t3.medium"]
    onDemandBaseCapacity: 0
    onDemandPercentageAboveBaseCapacity: 50
```

#### Two Node groups

```yaml
managedNodeGroups:
  - name: ng1-public
    instanceType: m5.xlarge
    instanceName: custom-node-name
    desiredCapacity: 4
  - name: ng2-private
    instanceType: m5.large
    desiredCapacity: 10
    privateNetworking: true
```

#### Custom vpc cidr

```yaml
vpc:
  cidr: 10.10.0.0/16
```

#### Existing VPC

```yaml
vpc:
  id: "vpc-0dd338ecf29863c55"
  subnets:
    private:
      us-west-2a:
          id: "subnet-0ff156e0c4a6d300c"
      us-west-2c:
          id: "subnet-0426fb4a607393184"
    public:
      us-west-2a:
          id: "subnet-0153e560b3129a696"
      us-west-2c:
          id: "subnet-009fa0199ec203c37"
```

#### NAT High Available

```yaml
vpc:
  nat:
    gateway: HighlyAvailable # other options: Disable, Single (default)

managedNodeGroups:
- name: nodegroup
  privateNetworking: true
```

#### CloudWatch cluster logging enable

```yaml
loudWatch:
  clusterLogging:
    enableTypes: ["audit", "authenticator", "controllerManager"]
    # all supported types: "api", "audit", "authenticator", "controllerManager", "scheduler"
    # supported special values: "*" and "all"
```
