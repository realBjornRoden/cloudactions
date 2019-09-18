# Create SOLO CentOS Linux Virtual Machine (VM)
* Command-Line Interface (CLI) [aws-cli](https://aws.amazon.com/cli/)
* JSON Query (jq) [jq-github-io](https://stedolan.github.io/jq/)

## Actions
1. Open a command line session using Terminal/xterm/putty or equiv
1. Run the `XXX` command to authenticate through the webui using an Account and Password
    ```
    $ XXX login
    ```
1. Use the `XXX` command to create a RESOURCE GROUP to host the VM and its other resources
    ```
   ```
1. Use the `XXX` command to display available RESOURCE GROUPS
    ```
    ```
1. Use the `XXX` command to create a VM; by default SSH will be allowed (firewall rule `XXXX`)
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
* Use the `XXX` command to shutdown a VM
    ```
    ```
* Use the `XXX` command to deallocate a VM
   ```
   ```
* Use the `XXX` command to delete a VM, not prompted for confirmation (`XXX` flag)
   ```
   ```
* Use the `XXX` command to start a stopped or deallocated VM
   ```
   ```
1. Use the `XXX` command to delete a RESOURCE GROUP and **all** resources assigned to it, here without confirmation (`XXX` flag)
    ```
    ```
***

* Prepare deciding the location for RESOURCE GROUP using the `XXX` command
```
```
* Prepare deciding the VM size using the `XXX` command, in this case selecting only 'XXX'
```
```
* Prepare deciding the VM IMAGE using the `XXX` command, in this case selecting only 'CentOS'
```
```
* Prepare deciding the ADMIN account using the `XXX` command with `--query` option
```
```
***

* Use the `XXX` command to show options
```
```
