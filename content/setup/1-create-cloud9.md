---
title: "Create a Cloud9 Env"
chapter: false
weight: 1
hidden: True
---

> The Cloud9 workspace should be built by an IAM user with Administrator privileges, not the root account user. Please ensure you are logged in as an IAM user, not the root account user

### Launch Cloud9 in your closest region

* Create a [Cloud9 Environment](https://us-west-2.console.aws.amazon.com/cloud9/home?region=us-west-2)
  * Select **Create environment**
  * Name it **eksworkshop**, click Next.
  * Choose **t3.small** for instance type, take all default values and click **Create environment**
* When it comes up, customize the environment by:
* Closing the **Welcome tab**
![c9before](https://www.eksworkshop.com/images/prerequisites/cloud9-1.png)
* Opening a new **terminal** tab in the main work area
![c9newtab](https://www.eksworkshop.com/images/prerequisites/cloud9-2.png)
* Closing the lower work area
![c9newtab](https://www.eksworkshop.com/images/prerequisites/cloud9-3.png)
* Your workspace should now look like this
![c9after](https://www.eksworkshop.com/images/prerequisites/cloud9-4.png)
