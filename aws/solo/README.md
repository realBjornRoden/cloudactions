# Create SOLO AMIv2 Linux Virtual Machine (VM)
* [AWS Documentation](https://docs.aws.amazon.com/index.html)
* Command-Line Interface (CLI) [aws-cli](https://aws.amazon.com/cli/)
* JSON Query (jq) [jq-github-io](https://stedolan.github.io/jq/)
* Launch a Linux Virtual Machine with Amazon EC2 [10-Minute Tutorials](https://aws.amazon.com/getting-started/tutorials/launch-a-virtual-machine/)

## Actions
1. Open a command line session using Terminal/xterm/putty or equiv

1. Ensure login key configuration is made with  the `aws configure` for the user with sufficient Policy permissions

1. Create SSH key pair with  `aws ec2 create-key-pair` 
   ```
   $ aws ec2 create-key-pair --key-name ec2-vmadmin-key --region us-east-2 > ec2-vmadmin-key.pem
   $ chmod 400 ec2-vmadmin-key.pem
   ```
1. Use the `aws ec2 run-instances` to create a VM; by default SSH will be allowed (firewall rule `XXXX`) [aws-ec2-run-instance](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html)
   ```   
   $ aws ec2 run-instances --region us-east-2 --image-id ami-00c03f7f7f2ec15c3 --instance-type t2.micro --key-name ec2-vmadmin-key --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=vm-solo-03}]' --output json > vm-solo-03.json
    ```
1. Use the `aws ec2 describe-instances` command to display details of the VM
[cli-usage-output](https://docs.aws.amazon.com/en_pv/cli/latest/userguide/cli-usage-output.html)
    ```
    $ aws ec2 describe-instances --region us-east-2 --query 'Reservations[*].Instances[*].{"2.Instance":InstanceId,"5.AvailabilityZone":Placement.AvailabilityZone,"1.Name":Tags[?Key==`Name`]|[0].Value,"3.InstanceType":InstanceType,"7.PrivateIpAddress":PrivateIpAddress,"8.PublicIpAddress":PublicIpAddress,"6.State":State.Name,"9.Hypervisor":Hypervisor,"4.Processors":CpuOptions.CoreCount}' --output table
    ---------------------------------------------------------------------------------------------------------------------------------------------------------------------
    |                                                                         DescribeInstances                                                                         |
    +------------+----------------------+-----------------+---------------+---------------------+----------+---------------------+---------------------+----------------+
    |   1.Name   |     2.Instance       | 3.InstanceType  | 4.Processors  | 5.AvailabilityZone  | 6.State  | 7.PrivateIpAddress  |  8.PublicIpAddress  | 9.Hypervisor   |
    +------------+----------------------+-----------------+---------------+---------------------+----------+---------------------+---------------------+----------------+
    |  vm-solo-03|  i-06ada2bc32da15446 |  t2.micro       |  1            |  us-east-2b         |  running |  172.31.30.25       |  18.189.21.160      |  xen           |
    +------------+----------------------+-----------------+---------------+---------------------+----------+---------------------+---------------------+----------------+
   ```
1. Use the `aws ec2 describe-instances` or the `aws ec2 describe-network-interfaces` commands to display IP-address of VMs
    ```
    $ aws ec2 describe-instances --region us-east-2 --query 'Reservations[*].Instances[*].[Tags[?Key==`Name`]|[0].Value,InstanceId,PrivateIpAddress,PublicIpAddress]' --output text
    vm-solo-03    i-06ada2bc32da15446    172.31.30.25    18.189.21.160    running

    $ aws ec2 describe-network-interfaces --region us-east-2 --query 'NetworkInterfaces[*].[Attachment.InstanceId,PrivateIpAddress,Association.PublicIp]' --output text
    i-06ada2bc32da15446    172.31.30.25    18.189.21.160
    
    $ alias lsvm="aws ec2 describe-instances --region us-east-2 --query 'Reservations[*].Instances[*].[Tags[?Key==\`Name\`]|[0].Value,InstanceId,PrivateIpAddress,PublicIpAddress,Placement.AvailabilityZone,State.Name]' --output text"
    
    $ awslsvm
    vm-solo-00    i-06ada2bc32da15446    172.31.30.25    18.189.21.160

    ```
1. Use the `aws ec2 describe-volumes` command to display the VM DISK ID
    ```
    $ AWS_DEFAULT_OUTPUT=table aws ec2 describe-volumes --region us-east-2 --query 'Volumes[*].[Attachments[0].InstanceId,AvailabilityZone,VolumeId,Size]'
    i-06ada2bc32da15446    us-east-2b    vol-0cc41c407d9deb7eb    8
    ```
1. Use SSH to login to the VM
<br><i>NB. Use the default [user name] (https://docs.aws.amazon.com/en_pv/AWSEC2/latest/UserGuide/connection-prereqs.html#connection-prereqs-get-info-about-instance), such as `ec2-user`</i>
   ```
   $ ssh -v -v -v -i "ec2-vmadmin-key.pem" ec2-user@ec2-3-16-167-39.us-east-2.compute.amazonaws.com
   OpenSSH_7.9p1, LibreSSL 2.7.3
   debug1: Reading configuration data /etc/ssh/ssh_config
   debug1: /etc/ssh/ssh_config line 48: Applying options for *
   debug1: Connecting to ec2-3-16-167-39.us-east-2.compute.amazonaws.com port 22.
   ssh: connect to host ec2-3-16-167-39.us-east-2.compute.amazonaws.com port 22: Operation timed out
   ```
   Customize the login environment on the VM
   ```
   ```
   Open port 80 for the VM with the associated SG ( Security Group); verify access
   ```
   $ 
  
   $ curl --silent -q http://40.87.10.112:80 | grep -i welcome
   ```
   Continue customize the VM...
   ```
   $ ssh -v -v -v -i "ec2-vmadmin-key.pem" ec2-user@ec2-3-16-167-39.us-east-2.compute.amazonaws.com
     
   $
   ```
   Clone the VM; deploy the cloned image of the VM; open firewall port 80; verify
   ```
   $ curl --silent -q http://13.92.112.221:80 | grep -i welcome
   ```
***
* Use the `aws ec2 start-instances` command to start a stopped VM
```
$ aws ec2 start-instances --instance-id i-06ada2bc32da15446 --region us-east-2
STARTINGINSTANCES    i-06ada2bc32da15446
CURRENTSTATE    0    pending
PREVIOUSSTATE    80    stopped
```
* Use the `aws ec2 stop-instances` command to shutdown a VM
 ```
$ aws ec2 stop-instances --instance-id i-06ada2bc32da15446 --region us-east-2
STOPPINGINSTANCES    i-06ada2bc32da15446
CURRENTSTATE    64    stopping
PREVIOUSSTATE    16    running
```
* Use the `aws ec2 stop-instances --hibernate` command to hibernate a VM
<br><i>NB. Require VM type supporting hibernation and configured at launch (`--hibernation-options '{"Configured": true}'`)</i>
```
```
* Use the `aws ec2 terminate-instances` command to delete a VM
<br><i>NB. After the instance is terminated, it remains visible on the console for a short while, and then the entry is deleted</i>
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

* Prepare deciding the VM LOCATION using the `aws ec2 describe-regions` command (if no default region is configured, specify one to access the information from); will show the regions  that  are  currently available (enabled for the account) [aws-regions](https://docs.aws.amazon.com/en_pv/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)
 ```
$ aws ec2 describe-regions --region us-east-1 --query "Regions[]" --output table
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
* Prepare deciding the VM SIZE using the `XXX` command, in this case selecting only `t2.micro` 
[ec2-instance-type](https://aws.amazon.com/ec2/instance-types/)
<br><i>NB. Require adding the Policy `AWSPriceListServiceFullAccess` to the GROUP</i>
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
* Prepare deciding the VM IMAGE using the `aws ec2 describe-images` command (region specific), in this case selecting only `ami-hvm-2.0` Amazon Machine Image (ami) Hardware Virtualized Machine (hvm) v2.0
```
$ aws ec2 describe-images --region us-east-1 --owners amazon --filters 'Name=name,Values=amzn2-ami-hvm-2.0.????????-x86_64-gp2' 'Name=state,Values=available' --output json | jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId'
ami-0b898040803850657

$ aws ec2 describe-images --region us-east-1 --owners amazon --filters 'Name=name,Values=*ami-hvm-2.?*' 'Name=state,Values=available' --output json | jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId'
ami-0b69ea66ff7391e80
```
***

* Use the `aws ec2 help` command to show options
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
