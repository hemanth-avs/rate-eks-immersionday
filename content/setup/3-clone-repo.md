+++
title = "Clone Repo"
date = 2019-10-28T11:40:22+11:00
weight = 3
+++

### 1. Clone the Immersion day Workshop Repository

In the bottom panel of your new Cloud9 IDE, you will see a terminal command line terminal open and ready to use. Run the following git command in the terminal to clone the necessary code to complete this tutorial:

```bash
cd ~
git clone https://github.com/hemanth-avs/rate-eks-immersionday.git
```

After cloning the repository, you’ll see that your project explorer now includes the files cloned.

In the terminal, change directory to the subdirectory for this workshop in the repo:

```bash
cd rate-eks-immersionday
```

### 2. Setup

Run some additional automated setup steps with the `setup` script:

```bash
script/setup
```

This script will

* delete some unneeded Docker images to free up disk space
* install some Docker-related authentication mechanisms that will be discussed later.

Make sure you see the “Success!” message when the script completes.

[//]: # (populate a DynamoDB table with some seed data, upload site assets to S3, and)

### 3. Update IAM Settings for Cloud9 Workspace

{{% notice info %}}
Cloud9 normally manages IAM credentials dynamically. This isn't currently compatible with the EKS IAM authentication, so we will disable it and rely on the IAM role instead.
{{% /notice %}}

```bash
aws cloud9 update-environment  --environment-id $C9_PID --managed-credentials-action DISABLE
rm -vf ${HOME}/.aws/credentials
```

### 4. Finally, we should configure our aws cli with our current region as default

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')

echo "export ACCOUNT_ID=${ACCOUNT_ID}" >> ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" >> ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### 5. Clone files Repository

```bash
cd ~
git clone https://github.com/hemanth-avs/rate-eks-immersionday.git
cd rate-eks-immersionday
```
