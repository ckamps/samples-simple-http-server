# AWS CloudFormation Template - Simple Apache HTTP Server in Auto Scaling Group

This AWS CloudFormation template demonstrates deployment of one or more EC2 instances with Apache HTTP Server deployed using Auto Scaling Groups (ASGs).

The CloudFormation template also demonstrates how to use instrument an Amazon Linux 2 instance so that you can monitor metrics and log data via Amazon CloudWatch.

## `samples-simple-http-server.yml`

Performs the heavy lifting of deploying the sample application stack, but does not assign enterprise standard tags to resources.

The template also configures a CloudWatch Agent within each EC2 instance so that sample metrics from within the Amazon Linux OS instance such as filesystem usage can be logged via CloudWatch.  The Apache HTTP server log files and other log files are streamed to a CloudWatch Logs group.

An EC2 instance role is established so that AWS Systems Manager can be used to manage the instances including use of Session Manager to provide SSH access without needing to open up port 22 and set up bastion hosts.

## `samples-simple-http-server-wrapper.yml`

This optional wrapper template that creates a nested stack using the template described above.  This parent stack sets the enterprise standard tags on the heavy lifting stack. Useful when deploying the stack via the AWS console and explained in the quick start mentioned below.

## Accessing the Sample Web Server

See the CloudFormation outputs to see the FQDN of the sample application.  If you deployed the sample application with a public facing load balancer, then you should be able to access the sample application from a browser.

If you deployed the sample application to an internal facing load balancer, then you can access one of the Linux instances and use `curl` to download the page.

## Accessing EC2 Instances via SSH and AWS Systems Manager Session Manager

You can use the AWS Session Manager [Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) feature to provide controlled remote access to your Linux and Windows EC2 instances via standard SSH and RDP protocols.  By using Session Manager, you can avoid configuring and maintaining bastion hosts and make your AWS environments more secure by removing explicit port 22 and 3389 from security groups and firewalls and using AWS IAM to control access.  Session Manager also provides audit logging of all access.

Since the Amazon Linux 2 EC2 instances already have the AWS Systems Manager agent package installed, there's no additional software required on Amazon Linux 2 EC2 instances.  To connect to an instance, simply access the EC2 service in the AWS management console, select the instance of interest, and select `Connect`. 

You might need to wait a few minutes after an instance has been created to be able to access it via Session Manager.

See the [CloudFormation template](./samples-simple-http-server.yml) for how the IAM role associated with the EC2 instance profile is configured to enable Systems Manager service to monitor and manage the EC2 instances.


## Helper Scripts

### `scripts/manage-stack`

Creates or updates the sample stack using the heavy lifting template.  Sets enterprise standard tags when the stack is created.

### `scripts/copy-to-s3`

Copies the template files to the infrastructure artifact S3 repository.  Requires sufficient write access to that bucket.

## Parameters

These parameters apply to the heavy lifting stack `samples-simple-http-server.yml`.

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|**System Classification and Environment**| | | |
|`pOrg`|Optional|As an example of using resource naming standards, include the business organization in the names of resources including, for example, IAM roles.|`example`|
|`pSystem`|Optional|As an example of using resource naming standards, include a system identifier in the names of resources including, for example, IAM roles..|`samples`|
|`pApp`|Optional|As an example of using resource naming standards, include an application identifier in the names of resources including, for example, IAM roles.|`vpngw`|
|`pEnvPurpose`|Required|As an example of using resource naming standards, include a purpose for this particulart instance of the stack in the names of resources including, for example, IAM roles.. For example, "dev1", "test", "1", etc.|None|
|**Network Configuration**| | | |
|`pVpcId`|Required|ID of the VPC to which to deploy the application.|None|
|`pAlbSubnetId1`|Optional|Subnet ID 1 or 2 in which to deploy the Autoscaling Group.||
|`pAlbSubnetId2`|Optional|Subnet ID 2 or 2 in which to deploy the Autoscaling Group.||
|`pWebSubnetId1`|Optional|Subnet ID 1 or 2 in which to deploy the Apache HTTP server.||
|`pWebSubnetId2`|Optional|Subnet ID 2 or 2 in which to deploy the Apache HTTP server.||
|`pAlbScheme`|Optional|`internal` or `internet-facing`. The scheme of the Application Load Balancer (ALB)|`internet-facing`|
|`pAlbIngressCidr`|Optional|CIDR block to restrict ingress access.||
|`pAlbIngressPrefixListId`|Optional|Prefix list ID to restrict ingress access.||
|**EC2 Instance Configuration**| | | |
|`pServerCount`|Optional|Number of EC2 instances to spin up as part of the ASG|`1`|
|`pAmiId`|Optional|EC2 AMI ID.|Resolved value of SSM parameter: `/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-ebs`|
|`pInstanceType`|Optional|EC2 instance type. See the template for supported types|`t3a.micro`|
|**Other**| | | ||
`pPermissionsBoundaryArn`|Optional|ARN of an IAM Permissions boundary policy if you are required to use a permissions boundary whenever you create an IAM role.||