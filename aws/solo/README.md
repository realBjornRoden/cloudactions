# Create SOLO AMIv2 Linux Virtual Machine (VM)
* Command-Line Interface (CLI) [aws-cli](https://aws.amazon.com/cli/)
* JSON Query (jq) [jq-github-io](https://stedolan.github.io/jq/)

## Actions
1. Open a command line session using Terminal/xterm/putty or equiv
1. Ensure login key configuration is made with  the `aws configure`
1. (<i>optional)</i> Use the `aws ec2 create-placement-group` to create a PLACEMENT GROUP [aws-ec2-pg](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-placement-group.html)
   ```
   $ aws ec2 create-placement-group --group-name pg-useast-01 --strategy partition --partition-count 1 --region us-east-1
   ```
1. Use the `aws ec2 run-instances` command to create a VM; by default SSH will be allowed (firewall rule `XXXX`) [aws-ec2-run-instance](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html)
   ```
    ```
1. Use the `XXX` command to display IP-address of the VM
    ```
    ```
1. Use the `XXX` command to display the account resources
    ```
    ```
1. Use the `XXX` command to display the VM DISK ID
    ```
    ```

1. Use SSH to login to the VM
   ```
   $ ssh 40.87.10.112
   ```
   Customize the login environment on the VM
   ```
   [bjro@vm-solo-03 ~]$ cat >> .bashrc
   export LC_CTYPE=C
   export PS1="\u@\h:\w $ "
   set -o vi
   <CTRL-D>
  
   [bjro@vm-solo-03 ~]$ . ./.bashrc
  
   bjro@vm-solo-03:~ $ sudo yum -y update
   
   root@vm-solo-03:~ $ uname -a

   root@vm-solo-03:~ $ egrep -i 'processor|model n|cpu [mc]|flags' /proc/cpuinfo

   ```
   Customize the VM, such as installing software and basic verification that it's working
   ```
   bjro@vm-solo-03:~ $ sudo yum -y install gcc
   
   bjro@vm-solo-03:~ $ gcc --version
   
   bjro@vm-solo-03:~ $ python --version
   
   bjro@vm-solo-03:~ $ sudo bash
   
   root@vm-solo-03:/home/bjro $ cat > /etc/yum.repos.d/nginx.repo
   [nginx]
   name=nginx repo
   baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
   gpgcheck=0
   enabled=1
   
   root@vm-solo-03:/home/bjro $ yum -y install nginx
   
   root@vm-solo-03:/home/bjro $ systemctl enable nginx
   
   root@vm-solo-03:/home/bjro $ systemctl start nginx
   
   root@vm-solo-03:/home/bjro $ netstat -an|grep 'tcp.*80'
   
   root@vm-solo-03:/home/bjro $ curl --silent -q http://40.87.10.112:80 | grep -i welcome
  
   bjro@vm-solo-03:~ $ firewall-cmd --state

   bjro@vm-solo-03:~ $ exit
   ```
   Open port 80 for the VM with the associated SG ( Security Group); verify access
   ```
   $ az vm open-port --port 80 --resource-group rg-eastus-01 --name vm-solo-03 --output table
  
   $ curl --silent -q http://40.87.10.112:80 | grep -i welcome
   ```
   Continue customize the VM...
   ```
   $ ssh 40.87.10.112
     
   bjro@vm-solo-03:~ $ sudo systemctl start mariadb
   bjro@vm-solo-03:~ $ sudo systemctl enable mariadb

   bjro@vm-solo-03:~ $ awk -F: '{printf "%d,%s\n",$3,$1}' /etc/passwd >/tmp/users.csv
   
   bjro@vm-solo-03:~ $ mysql -u root -p
   
   MariaDB [(none)]> CREATE DATABASE userdb;
   Query OK, 1 row affected (0.00 sec)
   
   MariaDB [(none)]> MariaDB [(none)]> USE userdb;
   Database changed
   
   MariaDB [userdb]> CREATE TABLE IF NOT EXISTS users (
    ->     id INT PRIMARY KEY,
    ->     name VARCHAR(8) NOT NULL,
    ->     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    -> ); 
   Query OK, 0 rows affected (0.02 sec)
   
   MariaDB [userdb]> LOAD DATA LOCAL INFILE "/tmp/users.csv"
       -> INTO TABLE users
       -> FIELDS TERMINATED BY ","
       -> LINES TERMINATED BY "\n" ;
   Query OK, 27 rows affected, 29 warnings (0.01 sec)   
   Records: 27  Deleted: 0  Skipped: 0  Warnings: 29
   
   MariaDB [userdb]> SELECT * FROM users;
   +------+----------+---------------------+
   | id   | name     | created_at          |
   +------+----------+---------------------+
   |    0 | root     | 2019-09-17 13:19:41 |
   |    1 | bin      | 2019-09-17 13:19:41 |
   |    2 | daemon   | 2019-09-17 13:19:41 |
   |    3 | adm      | 2019-09-17 13:19:41 |
   ...
   | 1000 | bjro     | 2019-09-17 13:19:41 |
   +------+----------+---------------------+
   27 rows in set (0.00 sec)

   MariaDB [userdb]> DROP TABLE users;
   Query OK, 0 rows affected (0.00 sec)
   
   MariaDB [userdb]> DROP DATABASE userdb;
   Query OK, 0 rows affected (0.00 sec)
   
   MariaDB [(none)]> exit
   Bye
   ```
   Clone the VM; deploy the cloned image of the VM; open firewall port 80; verify
   ```
   $ curl --silent -q http://13.92.112.221:80 | grep -i welcome
   ```
***
* Use the `aws ec2 stop-instances` command to shutdown a VM
```
```
* Use the `aws ec2 stop-instances --hibernate` command to hibernate a VM
```
```
* Use the `aws ec2 terminate-instances` command to delete a VM
```
```
* Use the `aws ec2 start-instances` command to start a stopped or hibernated VM
```
```
* Use the `aws ec2 stop-instances` command to delete a VM
 ```
```
***

* Prepare deciding the location for the VM using the `aws ec2 describe-regions` command (if no default region is configured, specify one to access the information from); will show the regions  that  are  currently available (enabled for the account) [aws-regions](https://docs.aws.amazon.com/en_pv/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)
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
* Prepare deciding the VM size using the `XXX` command, in this case selecting only `t2.micro` 
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
* Prepare deciding the ADMIN account using the `XXX` command with `--query` option
```
```
***

* Use the `aws ec2 help` command to show options
```
```
