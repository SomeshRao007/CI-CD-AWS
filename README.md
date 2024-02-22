# CI-CD with AWS (basic)
# installation for AMAZON Linux 2 AMI

sudo yum update
sudo yum install ruby
sudo yum install wget

#for cleaning AMI for any previous agent caching


#!/bin/bash
CODEDEPLOY_BIN="/opt/codedeploy-agent/bin/codedeploy-agent"
$CODEDEPLOY_BIN stop
yum erase codedeploy-agent -y

# to install mannualy 

#git clone https://github.com/aws/aws-codedeploy-agent.git
#gem install bundler -v 1.3.5
#cd aws-codedeploy-agent
#bundle install
#rake clean && rake

to install from resource kit

wget https://bucket-name.s3.region-identifier.amazonaws.com/latest/install

#bucket-name is the name of the Amazon S3 bucket that contains the CodeDeploy Resource Kit files for your region, and region-identifier is the identifier for your region. 

#link: https://docs.aws.amazon.com/codedeploy/latest/userguide/resource-kit.html#resource-kit-bucket-names

# make it executable 
chmod +x ./install

# for installation 
sudo ./install auto

# changing DIR
cd /home/ec2-user

# checking Service
systemctl start codedeploy-agent
systemctl status codedeploy-agent




# for logs tail -f /var/log/aws/codedeploy-agent/codedeploy-agent.log


2024-02-22T10:21:00 INFO  [codedeploy-agent(2716)]: [Aws::CodeDeployCommand::Client 200 0.02356 0 retries] put_host_command_complete(command_status:"Failed",diagnostics:{format:"JSON",payload:"{\"error_code\":5,\"script_name\":\"\",\"message\":\"The CodeDeploy agent did not find an AppSpec file within the unpacked revision directory at revision-relative path \\\"appspec.yml\\\". The revision was unpacked to directory \\\"/opt/codedeploy-agent/deployment-root/1c2c8ea3-d1ea-4fe9-8bb9-37ea4a6e81df/d-D9JJ629M4/deployment-archive\\\", and the AppSpec file was expected but not found at path \\\"/opt/codedeploy-agent/deployment-root/1c2c8ea3-d1ea-4fe9-8bb9-37ea4a6e81df/d-D9JJ629M4/deployment-archive/appspec.yml\\\". Consult the AWS CodeDeploy Appspec documentation for more information at http://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html\",\"log\":\"\"}"},host_command_identifier:"eyJiYXRjaElkIjoiZjA5ZjgwYWFiYzRmZjdjMzYxMDU2NWZmZGQ2NjFkMjgvcHVibGljMDE3IiwiZGVwbG95bWVudElkIjoiQ29kZURlcGxveS91cy1lYXN0LTEvcHJvZC9vcnBoZXVzOnB1YmxpYzAwNC80Njk1NjM5NzA1ODM6ZC1EOUpKNjI5TTQiLCJob3N0SWQiOiJhcm46YXdzOmVjMjp1cy1lYXN0LTE6NDY5NTYzOTcwNTgzOmluc3RhbmNlL2ktMGNjMTc3N2RlOTliMDMzZTYiLCJjb21tYW5kSWQiOiJBcG9sbG9EZXBsb3lDb250cm9sU2VydmljZXxhcm46YXdzOmVjMjp1cy1lYXN0LTE6NDY5NTYzOTcwNTgzOmluc3RhbmNlL2ktMGNjMTc3N2RlOTliMDMzZTZ8M3wwIiwiY29tbWFuZE5hbWUiOiJCZWZvcmVJbnN0YWxsIiwiY29tbWFuZEluZGV4IjozLCJhdHRlbXB0SW5kZXgiOjF9")




