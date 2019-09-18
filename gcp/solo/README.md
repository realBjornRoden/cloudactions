# Create SOLO Debian Linux Virtual Machine (VM)
* The  Command-Line Interface ([CLI](https://cloud.google.com/sdk/docs/))
## Actions
1. Open a command line session using Terminal/xterm/putty or equiv

1. Display the current configuration with `gcloud info`  (set LC_CTYPE, if current setting is not supported by gcloud)
   ```
   $ LC_CTYPE=C gcloud info --format='text(config.account,config.project)'
   config.account: USER@FQDN
   config.project: None
   
   $ LC_CTYPE=C gcloud info
   ...
   ```
1. Display the accounts whose credentials are stored on the local system with `gcloud auth list` 
   ```
   $ gcloud auth list
   Credentialed Accounts
   ACTIVE  ACCOUNT
   *       USER@FQDN
   
   To set the active account, run:
   $ gcloud config set account `ACCOUNT`
   ```
1. Run the `gcloud init` to authenticate through the webui using an Account and Password
    ```
    $ gcloud init
    ```
1. Run the `gcloud projects list` to display current projects on the account
   ```
   $ gcloud projects list
   Listed 0 items.
   ```
1. Use the `gcloud projects create` to create a PROJECT to host the VM and its other resources
<br><i>NB. The project name must be unique.</i>
    ```
    $ create project-01-$RANDOM --name project-01 --enable-cloud-apis --set-as-default
    Create in progress for [https://cloudresourcemanager.googleapis.com/v1/projects/project-01-31482].
    Waiting for [operations/cp.6616306562793127147] to finish...done.
    Enabling service [cloudapis.googleapis.com] on project [project-01-31482]...
    Operation "operations/acf.7e200ba6-d443-43f0-9857-c3dce48a349f" finished successfully.
    Updated property [core/project] to [project-01-31482].
   ```
1. Display current projects on the account `gcloud projects list`
   ```
   $ gcloud projects list
   PROJECT_ID        NAME        PROJECT_NUMBER
   project-01-31482  project-01  827071269262
   ```
1. Display the current billing account `gcloud beta billing accounts list`
   ```
   $ gcloud beta billing accounts list
   ACCOUNT_ID            NAME                OPEN  MASTER_ACCOUNT_ID
   017776-DEAD6B-BEEF09  My Billing Account  True
   ```
1. Link the project with a billing account `gcloud beta billing accounts list`
   ```
   $ gcloud beta billing projects link project-01-31482 --billing-account 017776-DEAD6B-BEEF09
   billingAccountName: billingAccounts/017776-DEAD6B-BEEF09
   billingEnabled: true
   name: projects/project-01-31482/billingInfo
   projectId: project-01-31482
   ```
1. Display projects enabled for billing `gcloud beta billing projects list`
   ```
   $ gcloud beta billing projects list --billing-account 017776-5A3E6B-68C909
   PROJECT_ID        BILLING_ACCOUNT_ID    BILLING_ENABLED
   project-01-31482  017776-DEAD6B-BEEF09  True
   ```
1. Display APIs enabled for a project  `gcloud services list`
   ```
   $ gcloud services list --enabled --project project-01-31482
   NAME                              TITLE
   bigquery-json.googleapis.com      BigQuery API
   bigquerystorage.googleapis.com    BigQuery Storage API
   cloudapis.googleapis.com          Google Cloud APIs
   clouddebugger.googleapis.com      Stackdriver Debugger API
   cloudtrace.googleapis.com         Stackdriver Trace API
   datastore.googleapis.com          Cloud Datastore API
   logging.googleapis.com            Stackdriver Logging API
   monitoring.googleapis.com         Stackdriver Monitoring API
   servicemanagement.googleapis.com  Service Management API
   serviceusage.googleapis.com       Service Usage API
   sql-component.googleapis.com      Cloud SQL
   storage-api.googleapis.com        Google Cloud Storage JSON API
   storage-component.googleapis.com  Cloud Storage
   ```
1. Enable and verify API for project `gcloud services enable`
   ```
   $ gcloud services enable compute.googleapis.com --project project-01-31482
   
   $ gcloud services list --enabled --project project-01-31482 --filter "name:compute.googleapis.com"
   NAME                    TITLE
   compute.googleapis.com  Compute Engine API
   ```

1. Use the `gcloud compute instances create` to create a VM; by default SSH will be allowed (firewall rule `default-allow-ssh`)
    ```
    $ gcloud compute instances create vm-solo-03 --machine-type=f1-micro --zone=us-east1-b --project project-01-31482
    Created [https://www.googleapis.com/compute/v1/projects/project-01-31482/zones/us-east1-b/instances/vm-solo-03].
    NAME        ZONE        MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP    STATUS
    vm-solo-03  us-east1-b  f1-micro                   10.142.0.4   34.73.155.147  RUNNING
    ```
1. Use the `gcloud compute regions list` to display allocation  
    ```
   $ gcloud compute regions list --filter 'name=us-east1'
   NAME      CPUS  DISKS_GB  ADDRESSES  RESERVED_ADDRESSES  STATUS  TURNDOWN_DATE
   us-east1  1/8   10/2048   1/8        0/1                 UP
   ```
   This was the allocation prior to `gcloud compute instances create`:
   ```
   us-east1  0/8   0/2048    0/8        0/1                 UP
   ```
***
1. Use SSH to login to the VM `gcloud compute ssh`
   ```
   $ gcloud compute ssh vm-solo-03 --zone=us-east1-b --project project-01-31482
   WARNING: The public SSH key file for gcloud does not exist.
   WARNING: The private SSH key file for gcloud does not exist.
   WARNING: You do not have an SSH key for gcloud.
   WARNING: SSH keygen will be executed to generate a key.
   Generating public/private rsa key pair.
   Enter passphrase (empty for no passphrase): 
   Enter same passphrase again: 
   Your identification has been saved in /home/bjro/.ssh/google_compute_engine.
   Your public key has been saved in /home/bjro/.ssh/google_compute_engine.pub.
   The key fingerprint is:
   SHA256:3s/abjvwPOW0zjIH8JFRPeIYBmZO3zSY11N/mKtW934 bjro@FQDN
   The key's randomart image is:
   +---[RSA 2048]----+
   |         =. o+o.o|
   |        = .=+o.*o|
   |         ...=++ =|
   |          ..o. ..|
   |        S  o .o .|
   |       . .. ooo..|
   |        . .+o= ..|
   |           =O.+.E|
   |          .+*Oo o|
   +----[SHA256]-----+
   Updating project ssh metadata...⠹Updated [https://www.googleapis.com/compute/v1/projects/project-01-31482].                                                                         
   Updating project ssh metadata...done.                                                                                                                                               
   Waiting for SSH key to propagate.
   Warning: Permanently added 'compute.8936718379758117190' (ECDSA) to the list of known hosts.
   Linux vm-solo-03 4.9.0-11-amd64 #1 SMP Debian 4.9.189-3 (2019-09-02) x86_64
   
   The programs included with the Debian GNU/Linux system are free software;
   the exact distribution terms for each program are described in the
   individual files in /usr/share/doc/*/copyright.
   
   Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
   permitted by applicable law.
   bjro@vm-solo-03:~$ 
   ```
   Customize the login environment on the VM
   ```
   bjro@vm-solo-03:~$ cat >> .bashrc
   LC_CTYPE=C
   PS1="\u@\h:\w$ "
   set -o vi
   <CTRL-D>
  
   bjro@vm-solo-03:~$ . ./.bashrc
  
   bjro@vm-solo-03:~$ sudo apt-get update
   Get:1 http://security.debian.org stretch/updates InRelease [94.3 kB]
   Ign:2 http://deb.debian.org/debian stretch InRelease                            
   Get:3 http://packages.cloud.google.com/apt cloud-sdk-stretch InRelease [6,377 B]
   Get:4 http://deb.debian.org/debian stretch-updates InRelease [91.0 kB]                                     
   Get:5 http://packages.cloud.google.com/apt google-compute-engine-stretch-stable InRelease [3,843 B]                    
   Get:6 http://deb.debian.org/debian stretch-backports InRelease [91.8 kB]                         
   Hit:7 http://deb.debian.org/debian stretch Release                       
   Hit:8 http://packages.cloud.google.com/apt google-cloud-packages-archive-keyring-stretch InRelease
   Get:9 http://packages.cloud.google.com/apt cloud-sdk-stretch/main amd64 Packages [86.3 kB]
   Get:10 http://packages.cloud.google.com/apt google-compute-engine-stretch-stable/main amd64 Packages [1,315 B]
   Fetched 375 kB in 0s (543 kB/s)                    
   Reading package lists... Done
   
   root@vm-solo-03:~$ uname -a
   Linux vm-solo-03 4.9.0-11-amd64 #1 SMP Debian 4.9.189-3 (2019-09-02) x86_64 GNU/Linux

   root@vm-solo-03:~$ egrep -i 'processor|model n|cpu [mc]|flags' /proc/cpuinfo
   processor    : 0
   model name    : Intel(R) Xeon(R) CPU @ 2.30GHz
   cpu MHz        : 2300.000
   cpu cores    : 1
   flags        : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss ht syscall nx pdpe1gb rdtscp lm constant_tsc rep_good nopl xtopology nonstop_tsc pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm invpcid_single ssbd ibrs ibpb stibp kaiser fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid xsaveopt arat md_clear arch_capabilities
   ```
   Customize the VM, such as installing software and basic verification that it's working
   ```
   bjro@vm-solo-03:~$ sudo apt-get -y install gcc
   
   bjro@vm-solo-03:~$ gcc --version
   gcc (Debian 6.3.0-18+deb9u1) 6.3.0 20170516
   Copyright (C) 2016 Free Software Foundation, Inc.
   This is free software; see the source for copying conditions.  There is NO
   warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

   bjro@vm-solo-03:~$ python --version
   Python 2.7.13
      
   bjro@vm-solo-03:~$ sudo apt-get -y install nginx
      
   bjro@vm-solo-03:~$ netstat -an|grep 'tcp.*80'
   
   bjro@vm-solo-03:~$ curl --silent -q http://10.142.0.4:80 | grep -i welcome
   <title>Welcome to nginx!</title>
   <h1>Welcome to nginx!</h1>
   ```
   Open port 80 for the VM with the associated SG ( Security Group); verify access
   ```
   $ gcloud compute firewall-rules create --network=default default-allow-http --allow tcp:80 --project project-01-31482
   Creating firewall...⠧Created [https://www.googleapis.com/compute/v1/projects/project-01-31482/global/firewalls/default-allow-http].
   Creating firewall...done.
   NAME                NETWORK  DIRECTION  PRIORITY  ALLOW   DENY  DISABLED
   default-allow-http  default  INGRESS    1000      tcp:80        False
  
   $ curl --silent -q http://34.73.155.147:80 | grep -i welcome
   <title>Welcome to nginx!</title>
   <h1>Welcome to nginx!</h1>
   ```
   Continue customize the VM...
   ```
   $ gcloud compute ssh vm-solo-03 --zone=us-east1-b --project project-01-31482

   bjro@vm-solo-03:~$ sudo sudo apt install mariadb-server
   ...
   
   bjro@vm-solo-03:~$ sudo mysql_secure_installation
   
   NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
   SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!
   
   In order to log into MariaDB to secure it, we'll need the current
   password for the root user.  If you've just installed MariaDB, and
   you haven't set the root password yet, the password will be blank,
   so you should just press enter here.
   
   Enter current password for root (enter for none): 
   OK, successfully used password, moving on...
   
   Setting the root password ensures that nobody can log into the MariaDB
   root user without the proper authorisation.
   
   You already have a root password set, so you can safely answer 'n'.
   
   Change the root password? [Y/n] 
   New password: 
   Re-enter new password: 
   Password updated successfully!
   Reloading privilege tables..
   ... Success!
   
   By default, a MariaDB installation has an anonymous user, allowing anyone
   to log into MariaDB without having to have a user account created for
   them.  This is intended only for testing, and to make the installation
   go a bit smoother.  You should remove them before moving into a
   production environment.
   
   Remove anonymous users? [Y/n] 
   ... Success!
   
   Normally, root should only be allowed to connect from 'localhost'.  This
   ensures that someone cannot guess at the root password from the network.
   
   Disallow root login remotely? [Y/n] 
   ... Success!
   
   By default, MariaDB comes with a database named 'test' that anyone can
   access.  This is also intended only for testing, and should be removed
   before moving into a production environment.
   
   Remove test database and access to it? [Y/n] 
   - Dropping test database...
   ... Success!
   - Removing privileges on test database...
   ... Success!
   
   Reloading the privilege tables will ensure that all changes made so far
   will take effect immediately.
   
   Reload privilege tables now? [Y/n] 
   ... Success!
   
   Cleaning up...
   
   All done!  If you've completed all of the above steps, your MariaDB
   installation should now be secure.
   
   Thanks for using MariaDB!
  
   bjro@vm-solo-03:~ $ awk -F: '{printf "%d,%s\n",$3,$1}' /etc/passwd >/tmp/users.csv
   
   bjro@vm-solo-03:~ $ sudo mysql -u root -p
   Enter password: 
   Welcome to the MariaDB monitor.  Commands end with ; or \g.
   Your MariaDB connection id is 11
   Server version: 10.1.41-MariaDB-0+deb9u1 Debian 9.9
   
   Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   MariaDB [(none)]> CREATE DATABASE userdb;
   Query OK, 1 row affected (0.00 sec)
   
   MariaDB [(none)]> MariaDB [(none)]> USE userdb;
   Database changed
   
   MariaDB [userdb]> CREATE TABLE IF NOT EXISTS users (
    ->     id INT PRIMARY KEY,
    ->     name VARCHAR(8) NOT NULL,
    ->     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    -> ); 
   Query OK, 0 rows affected (0.06 sec)
   
   MariaDB [userdb]> LOAD DATA LOCAL INFILE "/tmp/users.csv"
       -> INTO TABLE users
       -> FIELDS TERMINATED BY ","
       -> LINES TERMINATED BY "\n" ;
   Query OK, 28 rows affected, 32 warnings (0.04 
   Records: 28  Deleted: 0  Skipped: 0  Warnings: 32
   
   MariaDB [userdb]> SELECT * FROM users;
   +-------+----------+---------------------+
   | id    | name     | created_at          |
   +-------+----------+---------------------+
   |     0 | root     | 2019-09-18 18:00:48 |
   |     1 | daemon   | 2019-09-18 18:00:48 |
   |     2 | bin      | 2019-09-18 18:00:48 |
   |     3 | sys      | 2019-09-18 18:00:48 |
   ...
   |  1000 | bjro     | 2019-09-18 18:00:48 |
   | 65534 | nobody   | 2019-09-18 18:00:48 |
   +-------+----------+---------------------+
   28 rows in set (0.00 sec)

   MariaDB [userdb]> DROP TABLE users;
   Query OK, 0 rows affected (0.02 sec)
   
   MariaDB [userdb]> DROP DATABASE userdb;
   Query OK, 0 rows affected (0.00 sec)
   
   MariaDB [(none)]> exit
   Bye
   bjro@vm-solo-03:~ $ exit
   ```
   Clone the VM; deploy the cloned image of the VM with open firewall port 80; verify
   ```
   $ gcloud compute disks snapshot vm-solo-03 --project=project-01-31482 --snapshot-names=snapshot-1 --zone=us-east1-b --storage-location=us-east1
   Creating snapshot(s) snapshot-1...done.
   
   $ gcloud compute snapshots list
   NAME        DISK_SIZE_GB  SRC_DISK                     STATUS
   snapshot-1  10            us-east1-b/disks/vm-solo-03  READY
   
   $ gcloud compute --project "project-01-31482" disks create "instance-1" --size "10" --zone "us-central1-a" --source-snapshot "snapshot-1" --type "pd-standard"
   WARNING: You have selected a disk size of under [200GB]. This may result in poor I/O performance. For more information, see: https://developers.google.com/compute/docs/disks#performance.
   Created [https://www.googleapis.com/compute/v1/projects/project-01-31482/zones/us-central1-a/disks/instance-1].
   NAME        ZONE           SIZE_GB  TYPE         STATUS
   instance-1  us-central1-a  10       pd-standard  READY
   
   $ gcloud beta compute --project=project-01-31482 instances create vm-solo-04 --zone=us-central1-a --machine-type=f1-micro --subnet=default  --tags=http-server --disk=name=instance-1
   Created [https://www.googleapis.com/compute/beta/projects/project-01-31482/zones/us-central1-a/instances/vm-solo-04].
   NAME        ZONE           MACHINE_TYPE  PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP  STATUS
   vm-solo-04  us-central1-a  f1-micro                   10.128.0.2   34.68.79.30  RUNNING

   $ curl --silent -q http://34.68.79.30:80 | grep -i welcome
   <title>Welcome to nginx!</title>
   <h1>Welcome to nginx!</h1>
   ```
***
* Use `gcloud compute instances stop` to shutdown a VM
 ```
$ gcloud compute instances stop vm-solo-03 --zone=us-east1-b --project project-01-31482
 Stopping instance(s) vm-solo-03...done.
 Updated [https://compute.googleapis.com/compute/v1/projects/project-01-31482/zones/us-east1-b/instances/vm-solo-03].
```
* Use `gcloud compute instances start` to start a VM
```
$ gcloud compute instances start vm-solo-03 --zone=us-east1-b --project project-01-31482
Starting instance(s) vm-solo-03...done.
Updated [https://compute.googleapis.com/compute/v1/projects/project-01-31482/zones/us-east1-b/instances/vm-solo-03].
```
* Use `gcloud compute instances delete` to delete a VM, not prompted for confirmation (`--quiet` flag)
 ```
$ gcloud compute instances delete vm-solo-03 --zone=us-east1-b --project project-01-31482 --quiet
   Deleted [https://www.googleapis.com/compute/v1/projects/project-01-31482/zones/us-east1-b/instances/vm-solo-03].
```
* Use `gcloud projects delete` to delete a PROJECT and **all** resources assigned to it, here without confirmation (`--quiet` flag)
 ```
$ gcloud projects delete project-01-31482 --quiet
Deleted [https://cloudresourcemanager.googleapis.com/v1/projects/project-01-31482].
    
You can undo this operation for a limited period by running '
commands below. See https://cloud.google.com/resource-manager/docs/creating-managing-projects for information on shutting down projects
    $ gcloud projects undelete project-01-31482
```
***

* Prepare deciding the VM LOCATION using `gcloud compute zones list` and `gcloud compute zones list`, in this case only in specific regions and zones
```
$ gcloud compute regions list --filter 'name~us-east* AND status=UP'
NAME      CPUS  DISKS_GB  ADDRESSES  RESERVED_ADDRESSES  STATUS  TURNDOWN_DATE
us-east1  0/8   0/2048    0/8        0/1                 UP
us-east4  0/8   0/2048    0/8        0/1                 UP

$ gcloud compute zones list --filter 'name~us-east* AND status=UP'
NAME        REGION    STATUS  NEXT_MAINTENANCE  TURNDOWN_DATE
us-east1-b  us-east1  UP
us-east1-c  us-east1  UP
us-east1-d  us-east1  UP
us-east4-c  us-east4  UP
us-east4-b  us-east4  UP
us-east4-a  us-east4  UP
```
* Prepare deciding the VM size using `gcloud compute machine-types list`, in this case only  'f1-micro' in specific zones
```
$ gcloud compute machine-types list --filter 'name=f1-micro AND zone~us-east*'
NAME      ZONE        CPUS  MEMORY_GB  DEPRECATED
f1-micro  us-east1-c  1     0.60
f1-micro  us-east1-d  1     0.60
f1-micro  us-east1-b  1     0.60
f1-micro  us-east4-a  1     0.60
f1-micro  us-east4-c  1     0.60
f1-micro  us-east4-b  1     0.60
```
* Prepare deciding the VM IMAGE using `gcloud compute images list`, in this case selecting only 'centos' (case sensitive)
```
$ gcloud compute images list --filter 'family~centos*'
NAME                PROJECT       FAMILY    DEPRECATED  STATUS
centos-6-v20190916  centos-cloud  centos-6              READY
centos-7-v20190916  centos-cloud  centos-7              READY
```

***

* Use `gcloud compute --help` to show options
```
$ gcloud compute --help
NAME
gcloud compute - create and manipulate Google Compute Engine resources

SYNOPSIS
gcloud compute GROUP | COMMAND [GCLOUD_WIDE_FLAG ...]

DESCRIPTION
The gcloud compute command group lets you create, configure and manipulate
Google Compute Engine virtual machines.

With Compute Engine you can create and run virtual machines on Google
infrastructure. Compute Engine offers scale, performance, and value that
allows you to easily launch large compute clusters on Google's
infrastructure.

More information on Compute Engine can be found here:
https://cloud.google.com/compute/ and detailed documentation can be found
here: https://cloud.google.com/compute/docs/

GCLOUD WIDE FLAGS
These flags are available to all commands: --account, --billing-project,
--configuration, --flags-file, --flatten, --format, --help,
--impersonate-service-account, --log-http, --project, --quiet,
--trace-token, --user-output-enabled, --verbosity. Run $ gcloud help for
details.

GROUPS
GROUP is one of the following:
...

COMMANDS
COMMAND is one of the following:
...

NOTES
These variants are also available:

$ gcloud alpha compute
$ gcloud beta compute
```
