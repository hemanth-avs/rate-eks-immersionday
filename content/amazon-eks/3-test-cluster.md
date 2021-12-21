---
title: "Test the Cluster"
weight: 3
---

#### Test the cluster:

Confirm your nodes:

```bash
kubectl get nodes # if we see response, we know we have authenticated correctly
```

#### Export the Stackname / Worker Role Name for use throughout the workshop

```bash
STACK_NAME=$(eksctl get cluster eksworkshop-eksctl -o json | jq '.[].Tags."aws:cloudformation:stack-name"')

echo "export STACK_NAME=${STACK_NAME}" | tee -a ~/.bash_profile
source ~/.bash_profile

FrgtPodExecRole=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId' | grep -i Fargate )

echo "export FrgtPodExecRole=${FrgtPodExecRole}" | tee -a ~/.bash_profile
```

#### Congratulations

You now have a fully working Amazon EKS Cluster that is ready to use!
