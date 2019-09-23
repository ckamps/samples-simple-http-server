# CloudFormation Template - Simple Apache HTTP Server in Auto Scaling Group

These CloudFormation templates demonstrate deployment of one or more EC2 instances with Apache HTTP Server deployed using Auto Scaling Groups (ASGs).

## `samples-simple-http-server.yml`

Performs the heavy lifting of deploying the sample application stack, but does not assign enterprise standard tags to resources.

The template also configures a CloudWatch Agent within each EC2 instances so that sample metrics from within the Amazon Linux OS instance such as filesystem usage can be logged via CloudWatch.

An EC2 instance role is established so that AWS Systems Manager can be used to manage the instances including use of Session Manager to provide SSH access without needing to open up port 22 and set up bastion hosts.

## `samples-simple-http-server-wrapper.yml`

Optional wrapper template that creates a nested stack using the template described above.  This parent stack sets the enterprise standard tags on the heavy lifting stack. Useful when deploying the stack via the AWS console and explained in the quick start mentioned below.

## Sample Application Quick Start

See the following documentation on how to launch the sample application, test it and take a tour of the resulting environment and supporting AWS services:

https://...

## Helper Scripts

### `scripts/manage-stack`

Creates or updates the sample stack using the heavy lifting template.  Sets enterprise standard tags when the stack is created.

### `scripts/copy-to-s3`

Copies the template files to the infrastructure artifact S3 repository.  Requires sufficient write access to that bucket.

## Parameters

These parameters apply to the heavy lifting stack `samples-simple-http-server.yml`.

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|`pVpcId`|Required|ID of the VPC to which to deploy the application.|None|
|`pOrg`|Optional|Per AWS resource naming standard, the business organization to apply to resources such as IAM roles.|`acme`|
|`pSystem`|Optional|Per AWS resource naming standards, system ID to apply to AWS resources.|`samples`|
|`pApp`|Optional|Per AWS resource naming standards, app ID to apply to AWS resources.|`http`|
|`pEnvPurpose`|Optional|Per AWS resource naming standards, qualifier as to purpose of this instance.|None|
|`pAlbSubnetId1`|Optional|Subnet ID 1 or 2 in which to deploy the Autoscaling Group.||
|`pAlbSubnetId2`|Optional|Subnet ID 2 or 2 in which to deploy the Autoscaling Group.||
|`pWebSubnetId1`|Optional|Subnet ID 1 or 2 in which to deploy the Apache HTTP server.||
|`pWebSubnetId2`|Optional|Subnet ID 2 or 2 in which to deploy the Apache HTTP server.||
|`pAlbScheme`|Optional|`internal` or `internet-facing`. The scheme of the Application Load Balancer (ALB)|`internet-facing`|
|`pAlbIngressCidr`|Optional|CIDR block to restrict ingress access.||
|`pAlbIngressPrefixListId`|Optional|Prefix list ID to restrict ingress access.||
|`pServerCount`|Optional|Number of EC2 instances to spin up as part of the ASG|`1`|
|`pAmiId`|Optional|EC2 AMI ID.|Resolved value of SSM parameter: `/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-ebs`|
|`pInstanceType`|Optional|EC2 instance type. See the template for supported types|`t3a.micro`|