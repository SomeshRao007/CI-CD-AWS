# Cloud_native-CI-CD-pipeline-AWS
This is a comprehensive guide to installing the AWS CodeDeploy agent on Amazon Linux 2 AMIs, and configuring the CodePipeline so that the changes made in you application Github Repository and will be deployed automatically. It covers manual and recommended installation methods, service role creation, IAM instance profile setup, and AppSpec file configuration for GitHub repositories. Learn how to troubleshoot deployment errors and leverage the CodeDeploy agent for seamless application deployments.

**Architecture diagram:**

![Basic CICD ](https://github.com/SomeshRao007/CI-CD-AWS-Basic/assets/111784343/492183d2-b4d0-4a16-a648-24a8fc916e5d)


**Key Takeaways:**

- Install CodeDeploy agent using the resource kit for optimal results.
- Create service roles and IAM instance profiles for secure access.
- Utilize AppSpec files for streamlined deployments from GitHub.
- Troubleshoot deployment issues with log monitoring.

**Target Audience:**

- Developers and DevOps engineers deploying applications to AWS.
- Users seeking guidance on CodeDeploy agent installation and configuration on Amazon Linux 2.
- Anyone interested in streamlining application deployments using CodeDeploy.

# Installation of CodeDeploy agent for AMAZON Linux 2 AMI
```
sudo yum update
sudo yum install ruby
sudo yum install wget
```
### For cleaning AMI for any previous agent caching
```
#!/bin/bash
CODEDEPLOY_BIN="/opt/codedeploy-agent/bin/codedeploy-agent"
$CODEDEPLOY_BIN stop
yum erase codedeploy-agent -y
```
### To install mannualy 
i wouldn't really recommend doing this, because you might end up with bundler related issues. If you really want to want to do it here you go. 
```
git clone https://github.com/aws/aws-codedeploy-agent.git
gem install bundler -v 1.3.5
cd aws-codedeploy-agent
bundle install
rake clean && rake
```
### To install from resource kit (recommended)
```
wget https://bucket-name.s3.region-identifier.amazonaws.com/latest/install
```
_bucket-name_ is the name of the Amazon S3 bucket that contains the CodeDeploy Resource Kit files for your region, and _region-identifier_ is the identifier for your region. 

for example in my case i was using N.Virginia so it was something like: 
```
wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/latest/install 
```

**link to Read about it:** https://docs.aws.amazon.com/codedeploy/latest/userguide/resource-kit.html#resource-kit-bucket-names

**Then make it executable by using:**
```
chmod +x ./install
```
**For installation:**

```sudo ./install auto```

**changing DIR:** 

```cd /home/ec2-user```

**checking Service:** 
```
systemctl start codedeploy-agent
systemctl status codedeploy-agent
```
## To check logs (Useful for debuging)

> I was facing some errors initially,

Error code:
Action execution failed

Error message:
Deployment d-Y6TWTZ4M4 failed

Deployment error code:
HEALTH_CONSTRAINTS

Deployment error message:
The overall deployment failed because too many individual instances failed deployment, too few healthy instances are available for deployment, or some instances in your deployment group are experiencing problems

> I used this command:

```
tail -f /var/log/aws/codedeploy-agent/codedeploy-agent.log
```
where,
_tail:_ This is a command-line utility used to view the last lines of a file.

_-f:_ This flag tells tail to follow the file, meaning it will continuously monitor the file and display new lines as they are added.

_/var/log/aws/codedeploy-agent/codedeploy-agent.log:_ This specifies the path to the log file you want to monitor. This file contains information about the activities of the CodeDeploy agent, including deployment status, errors, and events.

**Thanks to jNQ read more about it in his blog:** https://jqn.medium.com/debugging-codedeploy-health-constraints-error-903c773a010e


# Create a service role for CodeDeploy

Service roles are used to grant permissions to an AWS service so it can access AWS resources. The policies that you attach to the service role determine which resources the service can access and what it can do with those resources. 

The service role you create for **CodeDeploy** must be granted the permissions required for your compute platform. If you deploy to more than one compute platform, create one service role for each. 

**For EC2/On-Premises deployments**

Attach the _AWSCodeDeployRole_ policy.

If you want to read more about it or want to know how to do it using CLI refer to amazon docs: https://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-create-service-role.html

# Create an IAM instance profile for your Amazon EC2 instances

Your Amazon EC2 instances require permission to access the Amazon S3 buckets or GitHub repositories where the applications are hosted. To launch Amazon EC2 instances compatible with CodeDeploy, you must create an additional IAM role called an instance profile. These steps will show you how to create an IAM instance profile for your Amazon EC2 instances. This role allows the CodeDeploy agent to access the Amazon S3 buckets or GitHub repositories where your apps are hosted. 

policy name: _AmazonEC2RoleforAWSCodeDeploy_

Attach this pocily to your EC2 (you can do it even after creating your instance or while creating you can attach it)

if want to create your own policy and do it refer to aws docs: https://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-create-iam-instance-profile.html

# Configure CodePipeline

Configuring AWS CodePipeline for automatic deployments triggered by changes in your application Git repository involves several steps:

**Setting Up the Source Stage**

Choose your Git provider: Specify the Git service hosting your application code for example: GitHub, Gitlab, AWS CodeCommit.

Connect your repository: Authorize CodePipeline to access your code repository by providing necessary credentials.

Select the branch: Choose the specific branch containing your deployment code.

Enable event-based triggering: to Configure CodePipeline to automatically trigger deployments upon any code changes select _no filter_ option that will update any puch to your repo or you can select _specify filter_ to make changes on a specific action.


**Creating Build and Deployment Stages**

Build stage:

Define a build stage to process your code that is installing dependencies, compile code. Specify the build commands and environment. 
If you don't have any (just a basic HTML Page) then you can skip this and select aws code deploy.

Deployment stage:

Use AWS CodeDeploy to define a deployment stage to deploy your application to your chosen target EC2 instances or ECS containers.
then specify your application name and deployment group name in codepipeline.

Review and good to go just watch and enjoy the beautiful green green..


![Screenshot 2024-02-22 162540](https://github.com/SomeshRao007/CI-CD-AWS-Basic/assets/111784343/97e75c20-ba86-4646-b929-6009033cb398)



# Don't forget !!

- **Service roles:** Create IAM service roles for CodePipeline, CodeBuild, and CodeDeploy to grant necessary permissions.
- **IAM instance profiles**: For EC2 deployments, assign IAM instance profiles to your instances for access to resources.
- **Environment variables**: Manage environment variables for different deployment environments.
- And Lastly, in your github repository don't for get to add **AppSpec.yml file**, the AppSpec file is used by CodeDeploy to determine:
Your Amazon ECS task definition file. This is specified with its ARN in the TaskDefinition instruction in the AppSpec file.
The container and port in your replacement task set where your Application Load Balancer or Network Load Balancer reroutes traffic during a deployment. This is specified with the LoadBalancerInfo instruction in the AppSpec file.

Read more about it in amazon docs: https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html



