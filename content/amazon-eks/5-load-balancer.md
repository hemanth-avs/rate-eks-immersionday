---
title: "Setting up the LB controller"
weight: 5
draft: false
---

### AWS Load Balancer Controller

"AWS Load Balancer Controller" is a [controller](https://kubernetes.io/docs/concepts/architecture/controller/) to help manage Elastic Load Balancers for a Kubernetes cluster.

* It satisfies Kubernetes `Ingress` resources by provisioning Application Load Balancers.
* It satisfies Kubernetes `Service` resources by provisioning Network Load Balancers.

### set the AWS Load Balancer Controller version

```bash
echo 'export LBC_VERSION="v2.3.0"' >>  ~/.bash_profile
.  ~/.bash_profile
```

### Create IAM OIDC provider

First, we will have to set up an OIDC provider with the cluster.

This step is required to give IAM permissions to a Fargate pod running in the cluster using the IAM for Service Accounts feature.

{{% notice info %}}
Learn more about [IAM Roles for Service Accounts](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html) in the Amazon EKS documentation.
{{% /notice %}}

```bash
eksctl utils associate-iam-oidc-provider \
    --region ${AWS_REGION} \
    --cluster eksworkshop-eksctl \
    --approve
```

### Create an IAM policy

The next step is to create the IAM policy that will be used by the AWS Load Balancer Controller.

This policy will be later associated to the Kubernetes Service Account and will allow the controller pods to create and manage the ELB’s resources in your AWS account for you.

```bash
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/${LBC_VERSION}/docs/install/iam_policy.json

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

rm iam_policy.json
```

### Create a IAM role and ServiceAccount for the Load Balancer controller

Next, create a Kubernetes Service Account by executing the following command

```
eksctl create iamserviceaccount \
  --cluster eksworkshop-eksctl \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --approve
```

The above command deploys a CloudFormation template that creates an IAM role and attaches the IAM policy to it.

The IAM role gets associated with a Kubernetes Service Account. You can see details of the service account created with the following command.

```bash
kubectl get sa aws-load-balancer-controller -n kube-system -o yaml
```

Output

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::<AWS_ACCOUNT_ID>:role/eksctl-eksworkshop-eksctl-addon-iamserviceac-Role1-1DY83DJKXY0FT
  name: aws-load-balancer-controller
  namespace: kube-system
secrets:
- name: aws-load-balancer-controller-token-69q6s
```

{{% notice info %}}
For more information on IAM Roles for Service Accounts [follow this link](/beginner/110_irsa/).
{{% /notice %}}

### Install the TargetGroupBinding CRDs

```bash
kubectl apply -k "github.com/aws/eks-charts/stable/aws-load-balancer-controller/crds?ref=master"
```

```properties
customresourcedefinition.apiextensions.k8s.io/ingressclassparams.elbv2.k8s.aws created
customresourcedefinition.apiextensions.k8s.io/targetgroupbindings.elbv2.k8s.aws created
```

### Deploy the Helm chart from the Amazon EKS charts repo

```bash
helm repo add eks https://aws.github.io/eks-charts

export VPC_ID=$(aws eks describe-cluster \
                --name eksworkshop-eksctl \
                --query "cluster.resourcesVpcConfig.vpcId" \
                --output text)

helm upgrade -i aws-load-balancer-controller \
    eks/aws-load-balancer-controller \
    -n kube-system \
    --set clusterName=eksworkshop-eksctl \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller \
    --set image.tag=${LBC_VERSION} \
    --set region=${AWS_REGION} \
    --set vpcId=${VPC_ID}
```

You can check if the `deployment` has completed

```bash
kubectl -n kube-system rollout status deployment aws-load-balancer-controller
```
