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

* Create CloudWatch IAM policy

```bash
curl -o permissions.json https://raw.githubusercontent.com/aws-samples/amazon-eks-fluent-logging-examples/mainline/examples/fargate/cloudwatchlogs/permissions.json

aws iam create-policy --policy-name eks-fargate-logging-policy --policy-document file://permissions.json
```

* Attach the Cloudwatch Logs Policy to Fargate Role

```bash
echo $FrgtPodExecRole

aws iam attach-role-policy \
  --policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/eks-fargate-logging-policy \
  --role-name $FrgtPodExecRole
```

* Attach the Cloudwatch Agent Policy to Fargate Role

```bash
eksctl create iamserviceaccount \
 --name cwagent-prometheus \
 --namespace amazon-cloudwatch \
 --cluster eksworkshop-eksctl \
 --attach-policy-arn arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy \
 --approve \
 --override-existing-serviceaccounts
```

* Deploy Cloudwatch Agent

```bash
curl https://raw.githubusercontent.com/aws-samples/amazon-cloudwatch-container-insights/latest/k8s-deployment-manifest-templates/deployment-mode/service/cwagent-prometheus/prometheus-eks-fargate.yaml | 
sed "s/{{cluster_name}}/eksworkshop-eksctl/;s/{{region_name}}/$AWS_REGION/" | 
kubectl apply -f -
```
