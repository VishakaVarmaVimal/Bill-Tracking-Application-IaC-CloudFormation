# Infrastructure

## This repository contains template for AWS CloudFormation and commands to import an SSL certificate.
### Template for setting up network -

1. Install AWS CLI as the first step for this template. 
   Follow this link to install AWS CLI on Linux machine - https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html

2. Configure your AWS credentials using ```aws configure```
   Follow link https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html for detailed steps to setup a profile in your aws config

3. Create a copy of your own by forking the org by clicking on the `Fork` button (https://github.com/    varmavimalv-spring2020/infrastructure). All your work must be done in your forked repo.

4. Clone the forked repository by clicking on the `clone or download` button and select `Use SSH` (For example, I cloned using `git@github.com:VishakaVarmaVimal/webapp.git`)

## Resources (Read networking.json)

This template generates a Virtual Private Cloud (VPC) which is easily identifiable by a name provided by you while creating the stack. 

There are three subnets, each assigned to different `Availability Zone` in the same AWS region. (Say the region is us-east-1, then the subnets are assigned to availability zones us-east-1a, us-east-1b, us-east1c)

An Internet Gateway to access the network from the outside 

An Attach Internet Gateway to connect the IG and the VPC

A RouteTable to control the routing of outgoing and incoming network requests, each of the subnets is associated with the route table.

## Run the template

After installing and configuring your AWS credentials, and cloning this repository, run the following command on the command line -

### Create CloudFormation Stack 

```
aws cloudformation create-stack \
--profile dev \                                                                             #Optional
--stack-name <Stack_Name> \
--parameters ParameterKey=VPCCIDR,ParameterValue=<EnterCIDRValue> ParameterKey=MyVPCName,ParameterValue=<EnterVPCName> ParameterKey=AZ1,ParameterValue=<Region Specific AZ> ParameterKey=AZ2,ParameterValue=<Region Specific AZ> ParameterKey=AZ3,ParameterValue=<Region Specific AZ> ParameterKey=Subnet1CIDR,ParameterValue=<EnterCIDRValue> ParameterKey=Subnet2CIDR,ParameterValue=<EnterCIDRValue> ParameterKey=Subnet3CIDR,ParameterValue=<EnterCIDRValue> \
--template-body file://networking.json
```

### Delete CloudFormation Stack 

```aws cloudformation delete-stack --stack-name <Stack_Name>```


## Setup SSL Certificate

The following commands show how to import an SSL certificate to your AWS certificate manager from CLI.

When the certificate is purchased from a third party app (and not from AWS), use the following steps -

### Generate private key and CSR using OpenSSL

1. Check if your system has OpenSSL installed by typing ```openssl``` in your terminal. If it is not      installed, you must install it.
2. Enter the commands :

   ```openssl genrsa 2048 > private-key.pem``
   ```openssl req -new -key private-key.pem -out csr.pem```

3. Once the csr and private keys are generated, activate your SSL certificate from your provider using the csr. A file with certificate key and certificate bundle will be provided to you. Upload the certificate to your AWS using the following command -

   ```
  	$ aws acm import-certificate --certificate fileb://Certificate.pem \           
                                --certificate-chain fileb://CertificateChain.pem \
                                --private-key fileb://PrivateKey.pem \
                                --profile <profile_name> \
                                --region <region>```
   ```


## References 

https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html
https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#AddaSubnet
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-internetgateway.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc-gateway-attachment.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route-table.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route.html
https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route-table.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-join.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-internetgateway.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route.html
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc-gateway-attachment.html
https://docs.aws.amazon.com/acm/latest/userguide/import-certificate-api-cli.html
https://www.namecheap.com/support/knowledgebase/article.aspx/9592/14/generating-a-csr-on-amazon-web-services-aws/