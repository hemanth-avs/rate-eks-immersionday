---
title: "Fargate logging"
weight: 4
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

```yaml
---
kind: ConfigMap
apiVersion: v1
metadata:
  name: aws-logging
  namespace: aws-observability
data:
  output.conf: |
    [OUTPUT]
        Name cloudwatch_logs
        Match   *
        region us-west-2
        log_group_name fluent-bit-cloudwatch
        log_stream_prefix from-fluent-bit-
        auto_create_group true
        log_key log

  parsers.conf: |
    [PARSER]
        Name crio
        Format Regex
        Regex ^(?<time>[^ ]+) (?<stream>stdout|stderr) (?<logtag>P|F) (?<log>.*)$
        Time_Key    time
        Time_Format %Y-%m-%dT%H:%M:%S.%L%z
  
  filters.conf: |
     [FILTER]
        Name parser
        Match *
        Key_name log
        Parser crio
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
