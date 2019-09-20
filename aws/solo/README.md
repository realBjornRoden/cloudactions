# Create SOLO AMIv2 Linux Virtual Machine (VM)
* [AWS Documentation](https://docs.aws.amazon.com/index.html)
* Command-Line Interface (CLI) [aws-cli](https://aws.amazon.com/cli/)
* JSON Query (jq) [jq-github-io](https://stedolan.github.io/jq/)
* Launch a Linux Virtual Machine with Amazon EC2 [10-Minute Tutorials](https://aws.amazon.com/getting-started/tutorials/launch-a-virtual-machine/)

## Actions
1. Open a command line session using Terminal/xterm/putty or equiv

1. Ensure login key configuration is made with  the `aws configure` for the user with sufficient Policy permissions
   * Below is with performed with the group set to the policies <b>AmazonEC2FullAccess</b> and <b>AWSPriceListServiceFullAccess</b> (for the `aws pricing`)

1. Create SSH key pair with  `aws ec2 create-key-pair` 
   ```
   $ aws ec2 create-key-pair --key-name ec2-vmadmin-key --region us-east-2 > ec2-vmadmin-key.pem
   $ chmod 400 ec2-vmadmin-key.pem
   ```
1. Use the `aws ec2 run-instances` to create a VM; if not specified, the account default Security Group named `default` will apply [aws-ec2-run-instance](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html)
   ```   
   $ aws ec2 run-instances --region us-east-2 --image-id ami-00c03f7f7f2ec15c3 --instance-type t2.micro --key-name ec2-vmadmin-key --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=vm-solo-03}]' --output json > vm-solo-03.json
   ```
   Or without a name (in AWS the name is a tag and identification of a VM is through the <i>InstanceId</i>
   ```
   $ aws ec2 run-instances --region us-east-2 --image-id ami-0d8f6eb4f641ef691 --instance-type t2.micro --key-name ec2-vmadmin-key
    ```
1. Use the `aws ec2 describe-instances` to display details of the VM
[cli-usage-output](https://docs.aws.amazon.com/en_pv/cli/latest/userguide/cli-usage-output.html)
    ```
    $ aws ec2 describe-instances --region us-east-2 --query 'Reservations[*].Instances[*].{"2.Instance":InstanceId,"5.AvailabilityZone":Placement.AvailabilityZone,"1.Name":Tags[?Key==`Name`]|[0].Value,"3.InstanceType":InstanceType,"7.PrivateIpAddress":PrivateIpAddress,"8.PublicIpAddress":PublicIpAddress,"6.State":State.Name,"9.Hypervisor":Hypervisor,"4.Processors":CpuOptions.CoreCount}' --output table
    ------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    |                                                                           DescribeInstances                                                                          |
    +------------+----------------------+-----------------+---------------+---------------------+-------------+---------------------+---------------------+----------------+
    |   1.Name   |     2.Instance       | 3.InstanceType  | 4.Processors  | 5.AvailabilityZone  |   6.State   | 7.PrivateIpAddress  |  8.PublicIpAddress  | 9.Hypervisor   |
    +------------+----------------------+-----------------+---------------+---------------------+-------------+---------------------+---------------------+----------------+
    |  vm-solo-03|  i-0e5641b24d9618aa8 |  t2.micro       |  1            |  us-east-2b         |  running    |  172.31.26.155      |  3.16.167.39        |  xen           |
    +------------+----------------------+-----------------+---------------+---------------------+-------------+---------------------+---------------------+----------------+
   ```
1. Use the `aws ec2 describe-instances` or the `aws ec2 describe-network-interfaces` to display IP-address of VMs
    ```
    $ aws ec2 describe-instances --region us-east-2 --query 'Reservations[*].Instances[*].[Tags[?Key==`Name`]|[0].Value,InstanceId,PrivateIpAddress,PublicIpAddress]' --output text
    vm-solo-03    i-0e5641b24d9618aa8    172.31.26.155    3.16.167.39    running

    $ aws ec2 describe-network-interfaces --region us-east-2 --query 'NetworkInterfaces[*].[Attachment.InstanceId,PrivateIpAddress,Association.PublicIp]' --output text
    i-0e5641b24d9618aa8    172.31.26.155    3.16.167.39
    
    $ alias lsvm="aws ec2 describe-instances --region us-east-2 --query 'Reservations[*].Instances[*].[Tags[?Key==\`Name\`]|[0].Value,InstanceId,PrivateIpAddress,PublicIpAddress,Placement.AvailabilityZone,State.Name]' --output text"
    
    $ lsvm
    vm-solo-03    i-0e5641b24d9618aa8    172.31.26.155    3.16.167.39    us-east-2b    running
    ```
   1. Use the `aws ec2 authorize-security-group-ingress`  to enable SSH access to the VM
      * List the Security Group for the VM
      ```
      $ aws ec2 describe-instance-attribute --instance-id i-0e5641b24d9618aa8 --attribute groupSet --region us-east-2
      i-0e5641b24d9618aa8
      GROUPS    sg-ebf9c788
      ```
      * Display the ingress and egress specification for the VM assigned Security Group
      ```
      $ aws ec2 describe-security-groups --group-name default  --region us-east-2
      SECURITYGROUPS    default VPC security group    sg-ebf9c788    default    598691507898    vpc-5076823b
      IPPERMISSIONSEGRESS    -1
      IPRANGES    0.0.0.0/0
      ```
      * Display the ingress and egress specification for the `default` Security Group (same as above)
      ```
      $ aws ec2 describe-security-groups --group-name default  --region us-east-2
      SECURITYGROUPS    default VPC security group    sg-ebf9c788    default    598691507898    vpc-5076823b
      IPPERMISSIONSEGRESS    -1
      IPRANGES    0.0.0.0/0
      ```
      *  Get the workstation Internet external IP-address by probing `ifconfig.co` or equivalent service, if it don't work, temporarily use `--cidr "0.0.0.0/0`"
      ```
      $ curl ifconfig.co # or LANG=C wget -qO- ifconfig.co
      123.204.213.49
      ```
      * Set the Security Group VM ingress to allow SSH from the workstation Internet external IP-address
      ```
      $ aws ec2 authorize-security-group-ingress --group-id sg-ebf9c788 --protocol tcp --port 22 --cidr "123.204.213.49/24" --region us-east-2
      ```
      * Display the Security Group VM ingress and egress specification for the VM assigned Security Group
      ```
      $  aws ec2 describe-security-groups --group-id sg-ebf9c788  --region us-east-2
      SECURITYGROUPS    default VPC security group    sg-ebf9c788    default    598691507898    vpc-5076823b
      IPPERMISSIONS    22    tcp    22
      IPRANGES    123.204.213.0/24
      IPPERMISSIONSEGRESS    -1
      IPRANGES    0.0.0.0/0
      ```
1. Use the `aws ec2 describe-volumes` to display the VM DISK ID
    ```
    $ AWS_DEFAULT_OUTPUT=table aws ec2 describe-volumes --region us-east-2 --query 'Volumes[*].[Attachments[0].InstanceId,AvailabilityZone,VolumeId,Size]'
    i-06ada2bc32da15446    us-east-2b    vol-0cc41c407d9deb7eb    8
    ```
1. Use SSH to login to the VM
<br><i>NB. Use the default [user name] (https://docs.aws.amazon.com/en_pv/AWSEC2/latest/UserGuide/connection-prereqs.html#connection-prereqs-get-info-about-instance), such as `ec2-user`.</i>
   ```
   $ ssh -i "ec2-vmadmin-key.pem" ec2-user@ec2-3-16-167-39.us-east-2.compute.amazonaws.com
   The authenticity of host 'ec2-3-16-167-39.us-east-2.compute.amazonaws.com (3.16.167.39)' can't be established.
   ECDSA key fingerprint is SHA256:zJIOSADz3yfFt1045eNyS6lgOh4Nd47ATASrehF/UxY.
   Are you sure you want to continue connecting (yes/no)? yes
   Warning: Permanently added 'ec2-3-16-167-39.us-east-2.compute.amazonaws.com,3.16.167.39' (ECDSA) to the list of known hosts.
   
   __|  __|_  )
   _|  (     /   Amazon Linux 2 AMI
   ___|\___|___|
   
   https://aws.amazon.com/amazon-linux-2/
   7 package(s) needed for security, out of 10 available
   Run "sudo yum update" to apply all updates.
   [ec2-user@ip-172-31-26-155 ~]$ 
   ```
   Customize the login environment on the VM
   ```
   ```
   Open port 80 for the VM; verify access
   ```
   $ aws ec2 describe-instance-attribute --instance-id i-0e5641b24d9618aa8 --attribute groupSet --region us-east-2
   i-0e5641b24d9618aa8
   GROUPS    sg-ebf9c788
   
   $ aws ec2 authorize-security-group-ingress --group-id sg-ebf9c788 --protocol tcp --port 80 --cidr "0.0.0.0/0" --region us-east-2
  
   $ curl --silent -q http://3.16.167.39:80 | grep -i welcome
   ```
   Continue customize the VM...
   ```
   $ ssh -v -v -v -i "ec2-vmadmin-key.pem" ec2-user@ec2-3-16-167-39.us-east-2.compute.amazonaws.com
     
   $
   ```
   Clone the VM; deploy the cloned image of the VM; open firewall port 80; verify
   ```
   $ curl --silent -q http://3.16.167.39:80 | grep -i welcome
   ```
***
* Use the `aws ec2 start-instances` to start a stopped VM
```
$ aws ec2 start-instances --instance-id i-06ada2bc32da15446 --region us-east-2
STARTINGINSTANCES    i-06ada2bc32da15446
CURRENTSTATE    0    pending
PREVIOUSSTATE    80    stopped
```
* Use the `aws ec2 stop-instances` to shutdown a VM
 ```
$ aws ec2 stop-instances --instance-id i-06ada2bc32da15446 --region us-east-2
STOPPINGINSTANCES    i-06ada2bc32da15446
CURRENTSTATE    64    stopping
PREVIOUSSTATE    16    running
```
* Use the `aws ec2 stop-instances --hibernate` to hibernate a VM
<br><i>NB. Require VM type supporting hibernation and configured at launch (`--hibernation-options '{"Configured": true}'`).</i>
```
```
* Use the `aws ec2 terminate-instances` to delete a VM
<br><i>NB. After the instance is terminated, it remains visible on the console for a short while, and then the entry is deleted.</i>
```
$ aws ec2 terminate-instances --instance-id i-091e25934a7da1234 --region us-east-2
TERMINATINGINSTANCES    i-091e25934a7da1234
CURRENTSTATE    48    terminated
PREVIOUSSTATE    80    stopped

$ aws ec2 terminate-instances --instance-id i-06ada2bc32da15446 --region us-east-2
TERMINATINGINSTANCES    i-06ada2bc32da15446
CURRENTSTATE    32    shutting-down
PREVIOUSSTATE    16    running
```

***

* Prepare deciding the VM LOCATION using the `aws ec2 describe-regions` (if no default region is configured, specify one to access the information from); will show the regions  that  are  currently available (enabled for the account) [aws-regions](https://docs.aws.amazon.com/en_pv/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)
 ```
$ aws ec2 describe-regions --region us-east-2 --query "Regions[]" --output table
--------------------------------------------------------
|                    DescribeRegions                   |
+-----------------------------------+------------------+
|             Endpoint              |   RegionName     |
+-----------------------------------+------------------+
|  ec2.eu-north-1.amazonaws.com     |  eu-north-1      |
|  ec2.ap-south-1.amazonaws.com     |  ap-south-1      |
|  ec2.eu-west-3.amazonaws.com      |  eu-west-3       |
|  ec2.eu-west-2.amazonaws.com      |  eu-west-2       |
|  ec2.eu-west-1.amazonaws.com      |  eu-west-1       |
|  ec2.ap-northeast-2.amazonaws.com |  ap-northeast-2  |
|  ec2.ap-northeast-1.amazonaws.com |  ap-northeast-1  |
|  ec2.sa-east-1.amazonaws.com      |  sa-east-1       |
|  ec2.ca-central-1.amazonaws.com   |  ca-central-1    |
|  ec2.ap-southeast-1.amazonaws.com |  ap-southeast-1  |
|  ec2.ap-southeast-2.amazonaws.com |  ap-southeast-2  |
|  ec2.eu-central-1.amazonaws.com   |  eu-central-1    |
|  ec2.us-east-1.amazonaws.com      |  us-east-1       |
|  ec2.us-east-2.amazonaws.com      |  us-east-2       |
|  ec2.us-west-1.amazonaws.com      |  us-west-1       |
|  ec2.us-west-2.amazonaws.com      |  us-west-2       |
+-----------------------------------+------------------+
```
* Prepare deciding the VM SIZE using the `aws pricing get-attribute-values` , in this case selecting only `t2.micro` 
[ec2-instance-type](https://aws.amazon.com/ec2/instance-types/)
<br><i>NB. Require adding the Policy `AWSPriceListServiceFullAccess` to the GROUP.</i>, othwerise error:<br>
`An error occurred (AccessDeniedException) when calling the GetAttributeValues operation: User: arn:aws:iam::598691507898:user/ec2admin is not authorized to perform: pricing:GetAttributeValues`

```
$ aws pricing get-attribute-values --region us-east-1 --service-code=AmazonEC2 --attribute-name=instanceType |awk '/t2\./{print $2}'
t2.2xlarge
t2.large
t2.medium
t2.micro
t2.nano
t2.small
t2.xlarge
```
* Prepare deciding the VM IMAGE using the `aws ec2 describe-images` (region specific), in this case selecting only `ami-hvm-2.0` Amazon Machine Image (ami) Hardware Virtualized Machine (hvm) v2.0
```
$ aws ec2 describe-images --region us-east-2 --owners amazon --filters 'Name=name,Values=amzn2-ami-hvm-2.?.????????-x86_64-gp2' 'Name=state,Values=available' --output json | jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId'
ami-0d8f6eb4f641ef691

$ aws ec2 describe-images --image-ids ami-0d8f6eb4f641ef691 --region us-east-2 --output table
----------------------------------------------------------------------------
|                              DescribeImages                              |
+--------------------------------------------------------------------------+
||                                 Images                                 ||
|+---------------------+--------------------------------------------------+|
||  Architecture       |  x86_64                                          ||
||  CreationDate       |  2019-06-19T21:59:15.000Z                        ||
||  Description        |  Amazon Linux 2 AMI 2.0.20190618 x86_64 HVM gp2  ||
||  EnaSupport         |  True                                            ||
||  Hypervisor         |  xen                                             ||
||  ImageId            |  ami-0d8f6eb4f641ef691                           ||
||  ImageLocation      |  amazon/amzn2-ami-hvm-2.0.20190618-x86_64-gp2    ||
||  ImageOwnerAlias    |  amazon                                          ||
||  ImageType          |  machine                                         ||
||  Name               |  amzn2-ami-hvm-2.0.20190618-x86_64-gp2           ||
||  OwnerId            |  137112412989                                    ||
||  Public             |  True                                            ||
||  RootDeviceName     |  /dev/xvda                                       ||
||  RootDeviceType     |  ebs                                             ||
||  SriovNetSupport    |  simple                                          ||
||  State              |  available                                       ||
||  VirtualizationType |  hvm                                             ||
|+---------------------+--------------------------------------------------+|
|||                          BlockDeviceMappings                         |||
||+-----------------------------------+----------------------------------+||
|||  DeviceName                       |  /dev/xvda                       |||
||+-----------------------------------+----------------------------------+||
||||                                 Ebs                                ||||
|||+-------------------------------+------------------------------------+|||
||||  DeleteOnTermination          |  True                              ||||
||||  Encrypted                    |  False                             ||||
||||  SnapshotId                   |  snap-077085afe6b3ee68d            ||||
||||  VolumeSize                   |  8                                 ||||
||||  VolumeType                   |  gp2                               ||||
|||+-------------------------------+------------------------------------+|||
```
***

* Use the `aws ec2 help` to show options
```
NAME
ec2 -

DESCRIPTION
Amazon Elastic Compute Cloud (Amazon EC2) provides secure and resizable
computing capacity in the AWS cloud. Using Amazon  EC2  eliminates  the
need  to  invest  in  hardware  up front, so you can develop and deploy
applications faster.

To learn more about Amazon EC2, Amazon EBS, and  Amazon  VPC,  see  the
following resources:

o Amazon EC2 product page
o Amazon EC2 documentation
o Amazon EBS product page
o Amazon VPC product page
o Amazon VPC documentation

AVAILABLE COMMANDS
...
```
