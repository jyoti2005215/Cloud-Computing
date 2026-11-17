# Cloud Computing Lab -- Assignment 6

**Student:** Suvankar Pramanik\
**Programme:** B.Tech -- Computer Science and Engineering\
**Institution:** Adamas University\
**AWS console region used for IAM, EC2, VPC and CloudWatch:** Asia
Pacific (Mumbai), `ap-south-1`

## 1. Overview

This assignment covers Linux environment inspection and practical
exploration of AWS services: IAM, EC2, VPC, CloudWatch and AWS CLI.

## 2. Tasks Performed

### Task 1 -- Ubuntu/Linux Environment

The Ubuntu environment running in VirtualBox was inspected using:

``` bash
pwd
uname -a
whoami
ls -la
ls
df -h
free -h
```

These commands display the working directory, operating-system and
kernel information, current user, directory contents, filesystem
capacity, and memory/swap usage.

### Task 2 -- AWS Console and S3

AWS Management Console login was completed and the S3 practical was
performed as part of the assignment. Add the actual Task 2 bucket name,
operations and screenshots if required by the lab rubric. Never include
AWS credentials or secret keys in this repository.

### Task 3A -- Identity and Access Management (IAM)

The IAM console was explored.

  -----------------------------------------------------------------------
  Component                           Observation
  ----------------------------------- -----------------------------------
  IAM users                           One existing user, `demoadmin`

  IAM user groups                     No groups listed

  IAM roles                           Three service-linked roles visible

  IAM policies                        1,605 policies shown, including
                                      AWS-managed policies such as
                                      `AdministratorAccess`
  -----------------------------------------------------------------------

IAM manages authentication and authorization. Users and roles represent
identities, groups organize users, and policies define permissions.

### Task 3B -- Amazon EC2

The EC2 launch configuration was explored. The instance was **not
launched**.

  Parameter                 Observed configuration
  ------------------------- -----------------------------------------------
  Instance name             `cloudc2`
  AMI                       Canonical Ubuntu Server 26.04
  Architecture              64-bit (x86)
  Instance type             `t3.micro`
  vCPU / memory             2 vCPU / 1 GiB
  Key pair                  Proceed without a key pair (exploration only)
  VPC / subnet              Default VPC / no subnet preference
  Auto-assign public IP     Enabled
  Security group            New security group
  SSH                       Enabled, source shown as `0.0.0.0/0`
  HTTP / HTTPS              Not selected
  Root EBS volume           8 GiB, `gp3`, 3,000 IOPS
  Encryption                Not enabled in displayed configuration
  Additional file systems   None selected

Amazon EC2 provides resizable virtual compute capacity. An AMI supplies
the operating system and software configuration; an instance type
defines the compute resources.

**Security note:** `0.0.0.0/0` allows SSH access from any IPv4 address
and is not recommended for production. Restrict SSH to trusted IP
addresses and use an appropriate key pair for real deployments.

### Task 3C -- Amazon VPC

The VPC console was explored in the Mumbai region.

  Component           Observation
  ------------------- ----------------------------------
  VPCs                1 VPC shown as Available
  Subnets             3 subnets shown as Available
  Route tables        1 route table, marked as main
  Internet gateways   1 gateway, state Attached
  Security groups     1 security group named `default`

VPC ID shown:

``` text
vpc-0579ec6f2771a510c
```

Subnet IPv4 CIDR ranges shown:

``` text
172.31.32.0/20
172.31.0.0/20
172.31.16.0/20
```

-   **VPC:** Logically isolated virtual network in AWS.
-   **Subnet:** Network segment within a VPC.
-   **Route table:** Determines where network traffic is directed.
-   **Internet gateway:** Enables eligible VPC resources to communicate
    with the internet.
-   **Security group:** Virtual firewall controlling inbound and
    outbound traffic for associated resources.

### Task 4A -- Amazon CloudWatch

The CloudWatch console was opened in the Mumbai region. The dashboard,
Metrics navigation (including Classic metrics), and Alarms section were
explored. The dashboard indicated that no alarms, metrics or default
dashboard had been configured at the time of inspection.

CloudWatch provides monitoring and observability for AWS resources and
applications. It can collect metrics and logs, display monitoring data,
and evaluate alarm thresholds.

Examples of EC2 metrics include `CPUUtilization`, `NetworkIn` and
`NetworkOut`. Instance-specific metrics may not appear until an EC2
instance is running and publishing metrics.

### Task 4B -- AWS Command Line Interface (AWS CLI)

AWS CloudShell was opened from the AWS Management Console. An initial
attempt displayed an account-verification-in-progress message;
CloudShell subsequently opened successfully in the `ap-southeast-2`
region.

Commands used for CLI and identity verification:

``` bash
aws --version
aws sts get-caller-identity
aws s3 ls
```

A local sample file can be created using:

``` bash
mkdir -p cloud-lab6
cd cloud-lab6
echo "AWS CLI Cloud Computing Lab 6" > sample.txt
cat sample.txt
```

S3 upload, listing, download, verification and cleanup commands (replace
`YOUR-BUCKET-NAME` with the actual bucket name):

``` bash
# Upload
aws s3 cp sample.txt s3://YOUR-BUCKET-NAME/sample.txt

# List bucket objects
aws s3 ls s3://YOUR-BUCKET-NAME/

# Download
aws s3 cp s3://YOUR-BUCKET-NAME/sample.txt downloaded.txt

# Verify downloaded content
cat downloaded.txt

# Remove the test object
aws s3 rm s3://YOUR-BUCKET-NAME/sample.txt
```

If needed, specify the bucket's region, for example
`--region ap-south-1`.

AWS CLI is a unified command-line tool for interacting with AWS
services. AWS STS returns identity information for the current
credentials, and `aws s3` commands manage S3 buckets and objects.

**Evidence note:** Include the actual terminal outputs and screenshots.
Replace the bucket placeholder and only report upload/download/delete
operations as completed if they were successfully executed.

## 3. Key Learning Outcomes

1.  Linux commands for inspecting a virtual machine environment.
2.  AWS console access and navigation.
3.  IAM identities, groups, roles and policies.
4.  EC2 instance configuration, AMIs, instance types, networking and EBS
    storage.
5.  VPC networking components: subnets, route tables, internet gateways
    and security groups.
6.  CloudWatch monitoring concepts, metrics and alarms.
7.  AWS CLI commands for identity verification and S3 object operations.

## 4. Security and Cleanup

-   Never commit AWS passwords, access keys, secret access keys, session
    tokens or private key files.
-   Redact account identifiers and personal email addresses from
    screenshots shared publicly.
-   Do not leave test EC2 instances, EBS volumes, public IP addresses or
    other billable resources running unnecessarily.
-   Restrict SSH access to trusted IP addresses; avoid `0.0.0.0/0` in
    production.
-   Remove only test objects created for this exercise. Confirm bucket
    contents before deleting a bucket.
-   Follow the principle of least privilege for IAM permissions.

## 5. Conclusion

This lab provided practical exposure to the Linux environment and core
AWS services. IAM was explored for identity and access management, EC2
for virtual machine configuration, VPC for cloud networking, CloudWatch
for monitoring, and AWS CLI for command-line interaction with AWS
services. The exercises connected foundational cloud-computing concepts
with the AWS Management Console and command-line workflows.

## 6. Suggested Evidence Structure

``` text
Assignment-6/
├── README.md
└── screenshots/
    ├── task-1-linux/
    ├── task-2-s3/
    ├── task-3a-iam/
    ├── task-3b-ec2/
    ├── task-3c-vpc/
    ├── task-4a-cloudwatch/
    └── task-4b-aws-cli/
```

Add only screenshots actually captured. Redact sensitive account
information before sharing the repository publicly.
