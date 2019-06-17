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

## Usage

## Dependence on Standard VPC Stack

The heavy lifting stack template is built to be compatible with deploying the sample application to a VPC that has already been created using the `infra-vpc-multi-tier.yml` CloudFormation template.

https://github.com/ckamps/infra-aws-vpc-miulti-tier

The VPC multi-tier template exports a set of outputs to the account in which it is deployed.

This sample application template depends on the following outputs being exported in the account:

* `${pVpcName}-vpc-id`
* `${pVpcName}-${pAlbSubnetNameQualifier}-subnet-id-1`
* `${pVpcName}-${pAlbSubnetNameQualifier}-subnet-id-2`
* `${pVpcName}-${pWebServerSubnetNameQualifier}-subnet-id-1`
* `${pVpcName}-${pWebServerSubnetNameQualifier}-subnet-id-2`

## Helper Scripts

### `scripts/manage-stack`

Creates or updates the sample stack using the heavy lifting template.  Sets enterprise standard tags when the stack is created.

### `scripts/copy-to-s3`

Copies the template files to the infrastructure artifact S3 repository.  Requires sufficient write access to that bucket.

## Parameters

These parameters apply to the heavy lifting stack `samples-simple-http-server.yml`.

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|`pVpcName`|Required|name of the VPC to which to deploy the application.|None|
|`pOrg`|Optional|Per AWS resource naming standard, the business organization to apply to resources such as IAM roles.|`acme`|
|`pSystem`|Optional|Per AWS resource naming standards, system ID to apply to AWS resources.|`samples`|
|`pApp`|Optional|Per AWS resource naming standards, app ID to apply to AWS resources.|`http`|
|`pEnvPurpose`|Optional|Per AWS resource naming standards, qualifier as to purpose of this instance.|None|
|`pAlbSubnetNameQualifier`|Optional|The subnet name qualifier of the tier in which to deploy the Autoscaling Group. Used to construct references to the necessary exported CloudFormation outputs from the network stack.|`public`|
|`pWebServerSubnetNameQualifier`|Optional|The subnet name qualifier of the tier in which to deploy the Apache HTTP server. Used to construct references to the necessary exported CloudFormation outputs from the network stack.|`private`|
|`pAlbScheme`|Optional|`internal` or `internet-facing`. The scheme of the Application Load Balancer (ALB)|`internet-facing`|
|`pAlbIngressCidrIp`|Optional|CIDR block to restrict ingress access.|`0.0.0.0/0`|
|`pServerCount`|Optional|Number of EC2 instances to spin up as part of the ASG|`1`|
|`pInstanceType`|Optional|EC2 instance type. See the template for supported types|`t2.micro`|
