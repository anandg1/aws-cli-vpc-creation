# Creating an AWS VPC using AWS-CLI

Amazon’s Virtual Private Cloud (VPC) is a foundational AWS service. Each VPC creates an isolated virtual network environment in the AWS cloud, dedicated to your AWS account. Using VPC, you can quickly spin up a virtual network infrastructure that AWS instances can be launched into.

Let us see how to configure a VPC with 3 public subnets with necessary routes using AWS Command Line Interface.

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)]()

## PRE-REQUISITES:

- Basic knowledge of AWS services, VPC, IAM and AWS-CLI.
- IAM user with programmatic Access.
- AWS Command Line Interface [installed and configured](https://github.com/anandg1/aws-cli-configuration) on the system.

## STEPS:

#### 1) Creating a VPC with IP CIDR block of 172.32.0.0/16

```sh
aws ec2 create-vpc --cidr-block 172.32.0.0/16
```
##### Output:
```sh
{
    "Vpc": {
        "CidrBlock": "172.32.0.0/16",
        "DhcpOptionsId": "dopt-125cea79",
        "State": "pending",
        "VpcId": "vpc-0af5b23212a0a3df3",
        "OwnerId": "559283165071",
        "InstanceTenancy": "default",
        "Ipv6CidrBlockAssociationSet": [],
        "CidrBlockAssociationSet": [
            {
                "AssociationId": "vpc-cidr-assoc-09cd310ff6360b2f7",
                "CidrBlock": "172.32.0.0/16",
                "CidrBlockState": {
                    "State": "associated"
                }
            }
        ],
        "IsDefault": false
    }
}
```

#### 2) Updating the VPC with the a tag "production-vpc1" for proper identification in future.

```sh
aws ec2 create-tags --resources vpc-0af5b23212a0a3df3 --tags Key=Name,Value=production-vpc1
```

#### 3) Using the VPC ID from the above step, we are going to create the first subnet with a 172.32.0.0/18 CIDR block.

```sh
aws ec2 create-subnet --vpc-id vpc-0af5b23212a0a3df3 --cidr-block 172.32.0.0/18
```
creating the second subnet with a 172.32.64.0/18 CIDR block.

```sh
aws ec2 create-subnet --vpc-id vpc-0af5b23212a0a3df3 --cidr-block 172.32.64.0/18
```

creating the third subnet with a 172.32.128.0/18 CIDR block.
```sh
aws ec2 create-subnet --vpc-id vpc-0af5b23212a0a3df3 --cidr-block 172.32.128.0/18
```

##### Output:

```sh
root@AG:~# aws ec2 create-subnet --vpc-id vpc-0af5b23212a0a3df3 --cidr-block 172.32.0.0/18
{
    "Subnet": {
        "AvailabilityZone": "ap-south-1c",
        "AvailabilityZoneId": "aps1-az2",
        "AvailableIpAddressCount": 16379,
        "CidrBlock": "172.32.0.0/18",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-00dadb9637804a095",
        "VpcId": "vpc-0af5b23212a0a3df3",
        "OwnerId": "559283165071",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:ap-south-1:559283165071:subnet/subnet-00dadb9637804a095"
    }
}
root@AG:~# aws ec2 create-subnet --vpc-id vpc-0af5b23212a0a3df3 --cidr-block 172.32.64.0/18
{
    "Subnet": {
        "AvailabilityZone": "ap-south-1c",
        "AvailabilityZoneId": "aps1-az2",
        "AvailableIpAddressCount": 16379,
        "CidrBlock": "172.32.64.0/18",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-067e53968595e400a",
        "VpcId": "vpc-0af5b23212a0a3df3",
        "OwnerId": "559283165071",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:ap-south-1:559283165071:subnet/subnet-067e53968595e400a"
    }
}
root@AG:~# aws ec2 create-subnet --vpc-id vpc-0af5b23212a0a3df3 --cidr-block 172.32.128.0/18
{
    "Subnet": {
        "AvailabilityZone": "ap-south-1c",
        "AvailabilityZoneId": "aps1-az2",
        "AvailableIpAddressCount": 16379,
        "CidrBlock": "172.32.128.0/18",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-08e10cef07419043b",
        "VpcId": "vpc-0af5b23212a0a3df3",
        "OwnerId": "559283165071",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:ap-south-1:559283165071:subnet/subnet-08e10cef07419043b"
    }
}
```

#### 4) Creating an Internate Gateway to make the subnets public

Use the following command to create an IGW.
```sh
aws ec2 create-internet-gateway
```
- Adding a tag 'igw-prod' to the interet gateway
```sh
ec2 create-tags --resources igw-08d93738fe99eb1b9 --tags Key=Name,Value=igw-prod
```
##### Output:
```sh
{
    "InternetGateway": {
        "Attachments": [],
        "InternetGatewayId": "igw-08d93738fe99eb1b9",
        "OwnerId": "559283165071",
        "Tags": []
    }
}
```

##### Linking the internet gateway to VPC

Link the IGW to VPC using the following command:
```sh
aws ec2 attach-internet-gateway --vpc-id vpc-0af5b23212a0a3df3 --internet-gateway-id igw-08d93738fe99eb1b9 
```
#### 5) Creating a custom route table for the VPC.
A custom route table is created foe our VPC
```sh
aws ec2 create-route-table --vpc-id vpc-0af5b23212a0a3df3
```
##### Output:
```sh
{
    "RouteTable": {
        "Associations": [],
        "PropagatingVgws": [],
        "RouteTableId": "rtb-01a10df345c39f086",
        "Routes": [
            {
                "DestinationCidrBlock": "172.32.0.0/16",
                "GatewayId": "local",
                "Origin": "CreateRouteTable",
                "State": "active"
            }
        ],
        "Tags": [],
        "VpcId": "vpc-0af5b23212a0a3df3",
        "OwnerId": "559283165071"
    }
}
```
##### Pointing the route table to the IGW with all traffic (0.0.0.0/0)

Here I'm going to point the routetable to the IGW and is going to add a tag
```sh
aws ec2 create-route --route-table-id rtb-01a10df345c39f086 --destination-cidr-block 0.0.0.0/0 --gateway-id igw-08d93738fe99eb1b9 
```
##### Output:
```sh
{
    "Return": true
}
```
Adding tag:
```sh
aws ec2 create-tags --resources rtb-01a10df345c39f086 --tags Key=Name,Value=route-prod
```
Now, describe the route table to check whether it is created properly.
```sh
aws ec2 describe-route-tables --route-table-id rtb-01a10df345c39f086
```
##### Output:
```sh
{
    "RouteTables": [
        {
            "Associations": [],
            "PropagatingVgws": [],
            "RouteTableId": "rtb-01a10df345c39f086",
            "Routes": [
                {
                    "DestinationCidrBlock": "172.32.0.0/16",
                    "GatewayId": "local",
                    "Origin": "CreateRouteTable",
                    "State": "active"
                },
                {
                    "DestinationCidrBlock": "0.0.0.0/0",
                    "GatewayId": "igw-08d93738fe99eb1b9",
                    "Origin": "CreateRoute",
                    "State": "active"
                }
            ],
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "route-prod"
                }
            ],
            "VpcId": "vpc-0af5b23212a0a3df3",
            "OwnerId": "559283165071"
        }
```
#### 5) Linking route table with subnets.
Use the following command to check the subnets created by us:
```sh
aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-0af5b23212a0a3df3" --query "Subnets[*].{ID:SubnetId,CIDR:CidrBlock}"
```
##### Output:
```sh
[
    {
        "ID": "subnet-00dadb9637804a095",
        "CIDR": "172.32.0.0/18"
    },
    {
        "ID": "subnet-067e53968595e400a",
        "CIDR": "172.32.64.0/18"
    },
    {
        "ID": "subnet-08e10cef07419043b",
        "CIDR": "172.32.128.0/18"
    }
]
```
Now, associate the subnets to route table
```sh
aws ec2 associate-route-table  --subnet-id subnet-00dadb9637804a095 --route-table-id rtb-01a10df345c39f086
aws ec2 associate-route-table  --subnet-id subnet-067e53968595e400a --route-table-id rtb-01a10df345c39f086 
aws ec2 associate-route-table  --subnet-id subnet-08e10cef07419043b --route-table-id rtb-01a10df345c39f086 
```
##### Output:
```sh

{
    "AssociationId": "rtbassoc-0ab6b8a450c041bf0",
    "AssociationState": {
        "State": "associated"
    }
}

{
    "AssociationId": "rtbassoc-00c2579f5c823947c",
    "AssociationState": {
        "State": "associated"
    }
}

{
    "AssociationId": "rtbassoc-0985f9de2622546bc",
    "AssociationState": {
        "State": "associated"
    }
}
```
#### 6) Modifying the public access behaviour of subnet

Now, do the following to  modify the public IP addressing behavior of the subnet so that the instances launched in the subnets would automatically possess a public IP address.
```sh
aws ec2 modify-subnet-attribute --subnet-id subnet-00dadb9637804a095 --map-public-ip-on-launch
aws ec2 modify-subnet-attribute --subnet-id subnet-067e53968595e400a --map-public-ip-on-launch
aws ec2 modify-subnet-attribute --subnet-id subnet-08e10cef07419043b --map-public-ip-on-launch
```

