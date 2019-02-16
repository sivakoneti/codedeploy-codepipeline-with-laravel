# Deploying a Laravel application with AWS CodeDeploy and AWS CodePipeline

Hello, this is a self-paced workshop designed to explore the AWS Code Suite (AWS CodeCommit, AWS CodeDeploy and AWS CodePipeline).

## 1. Create Cloud9 instance for development

1.1\. Open the AWS Cloud9 console at https://console.aws.amazon.com/cloud9/.

1.2\. Click on **Create environment**.

1.3\. Type a Name of `MyDevelopmentInstance`, and choose **Next step**.

1.4\. Leave the default configuration and choose **Next step**.

1.5\. Click on **Create environment**.

1.6\. Now inside the **bash** terminal clone the reposiotry with `git clone https://github.com/aurbac/laravel-our-experiences.git`.

## 2. Create CodeCommit repository 

2.1\. Open the AWS CodeCommit console at https://console.aws.amazon.com/codesuite/codecommit/repositories.

2.2\. Click on **Create repository**.

2.3\. Type a Repository name of `OurExperiences`, and choose **Create**.

2.4\. Click on **Clone URL** and choose **Clone HTTPS**, the URL is now copied: https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OurExperiences

2.5\. Inside your Cloud9 environment got to the project folder `cd /home/ec2-user/environment/laravel-our-experiences/`.

2.6\. Configure Git credentials executing `git config --global credential.helper '!aws codecommit credential-helper $@'` and `git config --global credential.UseHttpPath true`.

2.7\. Add a new remote for the local project by executing `git remote add codecommit https://git-codecommit.us-east-1.amazonaws.com/v1/repos/OurExperiences`.

2.8\. And push to code commit with `git push codecommit`. Now you can refresh your CodeCommit repository and you will see the code.

## 3. Create IAM Role for EC2 Instance

3.1\. Open the IAM console at https://console.aws.amazon.com/iam/.

3.2\. In the navigation pane, choose Roles, **Create role**.

3.3\. On the **Select role type page**, choose **EC2** and the **EC2** use case. Choose **Next: Permissions**.

3.4\. On the **Attach permissions policy** page, select an AWS managed policy that grants your instances access to the resources that they need. In this case select **AWSCodeCommitReadOnly** and **AmazonEC2RoleforAWSCodeDeploy** by checking the checkbox. Choose **Next: Tags** and **Next: Review**

3.5\. On the Review page, type a name for the role `WebServerRole` and choose **Create role**.

## 4. Create EC2 Instance WebServer

4.1\. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.

4.2\. Choose **Launch Instance**.

4.3\. In **Step 1: Choose an Amazon Machine Image (AMI)**, find an **Amazon Linux 2 AMI (HVM), SSD Volume Type** at the top of the list and choose **Select**.

4.4\. In **Step 2: Choose an Instance Type**, choose **Next: Configure Instance Details**.

4.5\. In **Step 3: Configure Instance Details**, choose **Network**, and then choose the entry for your default VPC. It should look something like vpc-xxxxxxx (172.31.0.0/16) (default).

4.6\. For **Auto-assign Public IP** select **Enable**.

4.7\. For **IAM Role** select **WebServerRole** created previously.

4.8\. In **Advanced Details** expand the section, copy the content file of  and paste it in **User data** as text. 

4.9\. Choose **Next: Add Storage** and **Next: Add Tags**.

4.10\. In **Step 5: Add Tags**, choose **Add Tag**, in **Key** type `Name` and in **Value** type `WebServer`, add another tag with **Key** as `Environment` and **Value** as `Production`, and choose **Next: Configure Security Groups**.

4.11\. In **Step 6: Configure Security Group**, review the contents of this page, ensure that Assign a security group is set to Create a new security group, and verify that the inbound rule being created has the following default values.

Type: SSH
Protocol: TCP
Port Range: 22
Source: Anywhere 0.0.0.0/0

Type: HTTP
Protocol: TCP
Port Range: 80
Source: Anywhere 0.0.0.0/0

4.11\. Choose **Review and Launch**.

4.12\. In **Step 7: Review Instance Launch** choose **Launch**.

4.13\. Select the check box for the key pair that you created or create a new one, and then choose **Launch Instances**.

4.14\. Choose **View instances**.

## 5. Create a Service Role for AWS CodeDeploy

5.1\. Sign in to the AWS Management Console and open the IAM console at https://console.aws.amazon.com/iam/.

5.2\. In the navigation pane, choose **Roles**, and then choose **Create role**.

5.3\. On the **Create role** page, choose **AWS service**, and from the **Choose the service that will use this role** list, choose **CodeDeploy**.

5.4\. From **Select your use case**, choose **CodeDeploy** for EC2/On-Premises deployments.

5.5\. Choose **Next: Permissions**.

5.6\. On the **Attached permissions policies** page, the permission policy is displayed. Choose **Next: Tags** and choose **Next: Review**.

5.7\. On the **Review** page, in Role name type `CodeDeployServiceRole` and then choose **Create role**.

## 5. Create a AWS CodeDeploy application

5.1\. Open the AWS CodeDeploy console at https://console.aws.amazon.com/codedeploy/.

5.2\. Choose **Create application**.

5.3\. In **Application name**, type the name `OurExperiences` for your application.

5.4\. From **Compute Platform**, choose **EC2/On-premises**.

5.5\. Choose **Create application**.

5.6\. On your application page, from the **Deployment groups tab**, choose **Create deployment group**.

5.7\. In **Deployment group name**, type `Production`.

5.7\. In **Service role**, select the role `CodeDeployServiceRole` created previously.

5.8\. In **Environment configuration**, select **Amazon EC2 instances** and for **Key** type `Environment` and for **Value** type `Production`.

5.9\. For **Load balancer** uncheck **Enable load balancing** and choose **Create deployment group**.

## 5. Create a AWS Pipeline

5.1\. Open the AWS CodePipeline console at https://console.aws.amazon.com/codepipeline/.

5.2\. Choose **Create pipeline**.

5.3\. In **Choose pipeline settings**, for **Pipeline name** type `OurExperiences` and choose **Next**.

5.4\. In **Add source stage**, for **Source provider** select **AWS CodeCommit**.

5.5\. For **Repository name** select **OurExperiences** and for **Branch name** select **master**. Choose **Next**.

5.6\. In **Add build stage**, choose **Skip build stage** and click **Skip**.

5.7\. In **Add deploy stage**, for **Deploy provider** select **AWS CodeDeploy**, for **Application name** select **OurExperiences**, for **Deployment group** select **Production** and choose **Next**.

5.8\. Choose **Create pipeline**.