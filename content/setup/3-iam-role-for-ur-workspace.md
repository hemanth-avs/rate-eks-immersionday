---
title: "IAM Role for your workspace"
chapter: false
weight: 3
---

### Create IAM Role

1. Follow [this deep link to create an IAM role with Administrator access](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess&roleName=eksworkshop-admin).
1. Confirm that **AWS service** and **EC2** are selected, then click **Next: Permissions** to view permissions.
1. Confirm that **AdministratorAccess** is checked, then click **Next: Tags** to assign tags.
1. Take the defaults, and click **Next: Review** to review.
1. Enter **eksworkshop-admin** for the Name, and click **Create role**.
![createrole](https://www.eksworkshop.com/images/prerequisites/createrole.png)

### Attach the IAM role to your Workspace

1. Click the grey circle button (in top right corner) and select **Manage EC2 Instance**.
![cloud9Role](https://www.eksworkshop.com/images/prerequisites/cloud9-role.png)
1. Select the instance, then choose **Actions / Security / Modify IAM Role**
![c9instancerole](https://www.eksworkshop.com/images/prerequisites/c9instancerole.png)
1. Choose **eksworkshop-admin** from the **IAM Role** drop down, and select **Save**
![c9attachrole](https://www.eksworkshop.com/images/prerequisites/c9attachrole.png)

### Update IAM Settings for Cloud9 Workspace

{{% notice info %}}
Cloud9 normally manages IAM credentials dynamically. This isn't currently compatible with the EKS IAM authentication, so we will disable it and rely on the IAM role instead.
{{% /notice %}}

```bash
aws cloud9 update-environment  --environment-id $C9_PID --managed-credentials-action DISABLE
rm -vf ${HOME}/.aws/credentials
```
