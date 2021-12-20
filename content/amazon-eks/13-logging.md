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

