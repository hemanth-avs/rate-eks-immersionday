---
title: "Fargate logging"
weight: 13
---

* Amazon EKS on Fargate offers a built-in log router based on Fluent Bit
* All that you have to do is configure the log router. The configuration happens through a dedicated ConfigMap that must meet the following criteria:
  * Named aws-logging
  * Created in a dedicated namespace called aws-observability
* Stream logs from Fargate directly to destinations
  * Amazon CloudWatch
  * Amazon OpenSearch Service
  * Amazon S3
  * Amazon Kinesis Data Streams
  * partner tools through Amazon Kinesis Data Firehose

### Configure Builtin Log Router with ConfigMap

```bash
kubectl apply -f ~/rate-eks-immersionday/files/fargate-logging/aws-observability.yaml
```

### Provide Cloudwatch Logs permission to FargatePodExecutionRole

* Fetch the FargatePodExecution

```bash
aws cloudformation describe-stacks --stack-name "$STACK_NAME" | jq -r '[.Stacks[0].Outputs[] | {key: .OutputKey, value: .OutputValue}] | from_entries' > /tmp/cfn-output.json

export FrgtPodExecRoleARN=`cat /tmp/cfn-output.json | yq eval .FargatePodExecutionRoleARN -`

export FrgtPodExecRole=$(echo "$FrgtPodExecRoleARN" | sed "s/arn:aws:iam::641223728001:role\///" )
```

* Create CloudWatch IAM policy

```bash
curl -o permissions.json https://raw.githubusercontent.com/aws-samples/amazon-eks-fluent-logging-examples/mainline/examples/fargate/cloudwatchlogs/permissions.json

aws iam create-policy --policy-name eks-fargate-logging-policy --policy-document file://permissions.json
```

* Attach the Policy to Fargate Role

```bash
aws iam attach-role-policy \
  --policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/eks-fargate-logging-policy \
  --role-name $FrgtPodExecRole
```
