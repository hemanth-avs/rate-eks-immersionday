### Cloudwatch Monitoring

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
