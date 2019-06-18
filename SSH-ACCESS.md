# Accessing EC2 Instances via SSH and AWS Systems Manager Session Manager

You can use the AWS Session Manager [Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) feature to provide controlled remote access to your Linux and Windows EC2 instances via standard SSH and RDP protocols.  By using Session Manager, you can avoid configuring and maintaining bastion hosts and make your AWS environments more secure by removing explicit port 22 and 3389 from security groups and firewalls and using AWS IAM to control access.  Session Manager also provides audit logging of all access.

## Configuring EC2 Linux Instances for Use with Systems Manager

Since the Amazon Linux 2 EC2 instances already have the AWS Systems Manager agent package installed, there's no additional software resuired on Amazon Linux 2 EC2 instances.

See the [CloudFormation template](./samples-simple-http-server.yml) for how the IAM role associated with the EC2 instance profile is configured to enable Systems Manager service to monitor and manage the EC2 instances.

## Using Systems Manager to Access EC2 Linux Instances via SSH

...
