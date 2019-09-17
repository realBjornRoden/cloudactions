# Create SOLO CentOS Linux Virtual Machine (VM)
* The Azure Command-Line Interface (CLI) | Microsoft Docs
[azure-cli-lates](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest)
* Azure CLI Query with JMESPath
[azure-query](https://techcommunity.microsoft.com/t5/ITOps-Talk-Blog/How-to-query-Azure-resources-using-the-Azure-CLI/ba-p/360147)
* JMESPath Query
[jmespath-query](http://jmespath.org/examples.html)
## Actions
1. Open a command line session using Terminal/xterm/putty or equiv
1. Run the `az` command to authenticate through the webui using a Microsoft Account and Password
    ```
    $ az login
    ```
1. Use the `az group create` command to create a RESOURCE GROUP to host the VM and its other resources
    ```
    $ az group create --name rg-eastus-01 --location eastus
    {
      "id": "/subscriptions/SUBSCRIPTIONID/resourceGroups/rg-eastus-01",
      "location": "eastus",
      "managedBy": null,
      "name": "rg-eastus-01",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null,
      "type": null
    }
   ```
   Same but with the `--output table` flag (default is JSON formatted output)
   ```
   $ az group create --name rg-eastus-01 --location eastus --output table
   Location    Name
   ----------  ------------
   eastus      rg-eastus-01
    ```
1. Use the `az group list` command to display available RESOURCE GROUPS
    ```
    $ az group list --output table
    Name                              Location      Status
    --------------------------------  ------------  ---------
    rg-eastus-01                      eastus        Succeeded
    ...
    ```
1. Use the `az vm create` command to create a VM; by default SSH will be allowed (firewall rule `default-allow-ssh`)
    ```
    $ az vm create --resource-group rg-eastus-01 --name vm-solo-03 --image CentOS --admin-username bjro --generate-ssh-keys --output table
    ResourceGroup    PowerState    PublicIpAddress    Fqdns    PrivateIpAddress    MacAddress         Location    Zones
    ---------------  ------------  -----------------  -------  ------------------  -----------------  ----------  -------
    rg-eastus-01     VM running    40.87.10.112                10.0.0.4            00-0D-3A-1E-D2-CB  eastus
    ```
    Or the same with the `--verbose` flag, note the use of the existing SSH public key
    ```
    $ az vm create --resource-group rg-eastus-01 --name vm-solo-03 --image CentOS --admin-username bjro --generate-ssh-keys --output table --verbose
   Use existing SSH public key file: /Users/bjro/.ssh/id_rsa.pub
   Accepted: vm-solo-03 (Microsoft.Compute/virtualMachines)
   Succeeded: vm-solo-03VMNic (Microsoft.Network/networkInterfaces)
   Accepted: vm-solo-03VNET (Microsoft.Network/virtualNetworks)
   Accepted: vm-solo-03NSG (Microsoft.Network/networkSecurityGroups)
   Accepted: vm-solo-03PublicIP (Microsoft.Network/publicIPAddresses)
   Succeeded: vm-solo-03NSG (Microsoft.Network/networkSecurityGroups)
   Succeeded: vm-solo-03PublicIP (Microsoft.Network/publicIPAddresses)
   Succeeded: vm-solo-03VNET (Microsoft.Network/virtualNetworks)
   Accepted: vm-solo-03 (Microsoft.Compute/virtualMachines)
   ResourceGroup    PowerState    PublicIpAddress    Fqdns    PrivateIpAddress    MacAddress         Location    Zones
   ---------------  ------------  -----------------  -------  ------------------  -----------------  ----------  -------
   rg-eastus-01     VM running    40.87.10.112                10.0.0.4            00-0D-3A-1E-D2-CB  eastus
   Suppress exception unknown locale: UTF-8
   command ran in 132.913 seconds.
   ```
1. Use the `az vm show --show-details` command to display IP-address of the VM
    ```
    $ az vm show --show-details --resource-group rg-eastus-01 --name vm-solo-03 --output table
    Name        ResourceGroup    PowerState    PublicIps      Fqdns    Location    Zones
    ----------  ---------------  ------------  -------------  -------  ----------  -------
    vm-solo-03  rg-eastus-01     VM running    40.87.10.112           eastus  
    ```
1. Use the `az vm list-ip-addresses` command to display IP-address of the VM
   ```
   $ az vm list-ip-addresses --resource-group rg-eastus-01 --name vm-solo-03 --output table
   VirtualMachine    PublicIPAddresses    PrivateIPAddresses
   ----------------  -------------------  --------------------
   vm-solo-03        40.87.10.112        10.0.0.4
1. Use the `az resource list` command to display the account resources
    ```
    $ az resource list --output table
    Name                                                  ResourceGroup                     Location      Type                                     Status
    ----------------------------------------------------  --------------------------------  ------------  ---------------------------------------  --------
    vm-solo-03_OsDisk_1_b3180d7e75c54767ba5222f2c34494de  RG-EASTUS-01                      eastus        Microsoft.Compute/disks
    vm-solo-03                                            rg-eastus-01                      eastus        Microsoft.Compute/virtualMachines
    vm-solo-03VMNic                                       rg-eastus-01                      eastus        Microsoft.Network/networkInterfaces
    vm-solo-03NSG                                         rg-eastus-01                      eastus        Microsoft.Network/networkSecurityGroups
    vm-solo-03PublicIP                                    rg-eastus-01                      eastus        Microsoft.Network/publicIPAddresses
    vm-solo-03VNET                                        rg-eastus-01                      eastus        Microsoft.Network/virtualNetworks
    ```
1. Use the `az vm show` command to display the VM DISK ID
    ```
    $ az vm show -g rg-eastus-01 -n vm-solo-03 --query "storageProfile.osDisk.managedDisk.id" -o tsv
    /subscriptions/8e79d269-e904-4c8e-a3d8-5503f0e310e7/resourceGroups/rg-eastus-01/providers/Microsoft.Compute/disks/vm-solo-03_OsDisk_1_b3180d7e75c54767ba5222f2c34494de
    ```

1. Use SSH to login to the VM
   ```
   $ ssh 40.87.10.112
   The authenticity of host '40.87.10.112 (40.87.10.112)' can't be established.
   ECDSA key fingerprint is SHA256:UE4+kYUew2fY+1wSRyR3DV2WWlqxrbGe1KLX8lSiikI.
   Are you sure you want to continue connecting (yes/no)? yes
   Warning: Permanently added '40.87.10.112' (ECDSA) to the list of known hosts.
   [bjro@vm-solo-03 ~]$ 
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
   Loaded plugins: fastestmirror, langpacks
   Loading mirror speeds from cached hostfile
   No packages marked for update
   ```
   Customize the VM, such as installing software and basic verification that it's working
   ```
   bjro@vm-solo-03:~ $ sudo yum -y install gcc
   Loaded plugins: fastestmirror, langpacks
   Loading mirror speeds from cached hostfile
   Resolving Dependencies
   ...
   Complete!
   
   bjro@vm-solo-03:~ $ gcc --version
   gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-36)
   Copyright (C) 2015 Free Software Foundation, Inc.
   This is free software; see the source for copying conditions.  There is NO
   warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
   bjro@vm-solo-03:~ $ python --version
   Python 2.7.5
   
   bjro@vm-solo-03:~ $ sudo bash
   
   root@vm-solo-03:/home/bjro $ cat > /etc/yum.repos.d/nginx.repo
   [nginx]
   name=nginx repo
   baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
   gpgcheck=0
   enabled=1
   
   root@vm-solo-03:/home/bjro $ yum -y install nginx
   Loaded plugins: fastestmirror, langpacks
   Loading mirror speeds from cached hostfile
   nginx                                            | 2.9 kB  00:00:00     
   nginx/x86_64/primary_db                          | 154 kB  00:00:00     
   Resolving Dependencies
   ...
   Complete!
   
   root@vm-solo-03:/home/bjro $ systemctl enable nginx
   Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
   
   root@vm-solo-03:/home/bjro $ systemctl start nginx
   
   root@vm-solo-03:/home/bjro $ netstat -an|grep 'tcp.*80'
   tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN     
   
   root@vm-solo-03:/home/bjro $ curl --silent -q http://40.87.10.112:80 | grep -i welcome
   <title>Welcome to nginx!</title>
   <h1>Welcome to nginx!</h1>
  
   bjro@vm-solo-03:~ $ firewall-cmd --state
   not running

   bjro@vm-solo-03:~ $ exit
   ```
   Open port 80 for the VM with the associated NSG (Network Security Group)
   ```
   $ az vm open-port --port 80 --resource-group rg-eastus-01 --name vm-solo-03 --output table
   Location    Name           ProvisioningState    ResourceGroup    ResourceGuid
   ----------  -------------  -------------------  ---------------  ------------------------------------
   eastus      vm-solo-03NSG  Succeeded            rg-eastus-01     80b1b024-e6b9-44d2-9a6b-edd1c2c24aa2
  
   $ curl --silent -q http://40.87.10.112:80 | grep -i welcome
   <title>Welcome to nginx!</title>
   <h1>Welcome to nginx!</h1>
   
   $ ssh 40.87.10.112
     
   bjro@vm-solo-03:~ $ sudo systemctl start mariadb
   bjro@vm-solo-03:~ $ sudo systemctl enable mariadb
   Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
   bjro@vm-solo-03:~ $ sudo systemctl status mariadb
   * mariadb.service - MariaDB database server
      Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
      Active: active (running) since Tue 2019-09-17 12:52:53 UTC; 23s ago
   ...
   Sep 17 12:52:53 vm-solo-03 systemd[1]: Started MariaDB database server.

   bjro@vm-solo-03:~ $ sudo mysql_secure_installation
   
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
   
   Set root password? [Y/n] 
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
   
   bjro@vm-solo-03:~ $ mysql -u root -p
   Enter password: 
   Welcome to the MariaDB monitor.  Commands end with ; or \g.
   Your MariaDB connection id is 13
   Server version: 5.5.60-MariaDB MariaDB Server
   
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
   
   bjro@vm-solo-03:~ $ exit

   $
   ```
***
* Use the `az vm stop` command to shutdown a VM
    ```
    $ az vm stop --resource-group rg-eastus-01 --name vm-solo-02
    About to power off the specified VM...
    It will continue to be billed. To deallocate a VM, run: az vm deallocate.
    ```
* Use the `az vm deallocate` command to deallocate a VM
   ```
   $ az vm deallocate --resource-group rg-eastus-01 --name vm-solo-02 &
   
   $ az vm list -d --output table
   Name        ResourceGroup    PowerState       PublicIps      Fqdns    Location    Zones
   ----------  ---------------  ---------------  -------------  -------  ----------  -------
   vm-solo-02  RG-EASTUS-01     VM deallocating                          eastus
   vm-solo-03  RG-EASTUS-01     VM running       40.87.10.112            eastus
   $ sleep 240
   $ az vm list -d --output table
   Name        ResourceGroup    PowerState      PublicIps      Fqdns    Location    Zones
   ----------  ---------------  --------------  -------------  -------  ----------  -------
   vm-solo-02  RG-EASTUS-01     VM deallocated                          eastus
   vm-solo-03  RG-EASTUS-01     VM running      40.87.10.112            eastus
   ```
* Use the `az vm delete` command to delete a VM, not prompted for confirmation (`--yes` flag)
   ```
   $ az vm delete --yes --resource-group rg-eastus-01 --name vm-solo-02 &
   
   $ az vm list -d --output table
   Name        ResourceGroup    PowerState      PublicIps      Fqdns    Location    Zones
   ----------  ---------------  --------------  -------------  -------  ----------  -------
   vm-solo-02  RG-EASTUS-01     VM deallocated                          eastus
   vm-solo-03  RG-EASTUS-01     VM running      40.87.10.112            eastus
   $ sleep 60
   $ az vm list -d --output table
   Name        ResourceGroup    PowerState    PublicIps      Fqdns    Location    Zones
   ----------  ---------------  ------------  -------------  -------  ----------  -------
   vm-solo-03  RG-EASTUS-01     VM running    40.87.10.112            eastus
   ```
* Use the `az vm start` command to start a stopped or deallocated VM
   ```
   $ az vm start --resource-group rg-eastus-01 --name vm-solo-03 &
   
   $ while :;do az vm list -d --output table;sleep 5;done
      Name        ResourceGroup    PowerState      PublicIps      Fqdns    Location    Zones
   ----------  ---------------  --------------  -------------  -------  ----------  -------
   vm-solo-03  RG-EASTUS-01     VM running      40.87.10.112            eastus
   ...
   Name        ResourceGroup    PowerState      PublicIps    Fqdns    Location    Zones
   ----------  ---------------  --------------  -----------  -------  ----------  -------
   vm-solo-03  RG-EASTUS-01     VM deallocated                         eastus
   ...
   Name        ResourceGroup    PowerState    PublicIps       Fqdns    Location    Zones
   ----------  ---------------  ------------  --------------  -------  ----------  -------
   vm-solo-03  RG-EASTUS-01     VM running    40.121.193.177           eastus
   ```
1. Use the `az group delete` command to delete a RESOURCE GROUP and **all** resources assigned to it, here without confirmation (`--yes` flag)
    ```
    $ az group delete --name rg-eastus-01 --yes
    ```
***

* Prepare deciding the location for RESOURCE GROUP using the `az resource list-locations` command
```
$ az account list-locations --output table
DisplayName           Latitude    Longitude    Name
--------------------  ----------  -----------  ------------------
East Asia             22.267      114.188      eastasia
Southeast Asia        1.283       103.833      southeastasia
Central US            41.5908     -93.6208     centralus
East US               37.3719     -79.8164     eastus
East US 2             36.6681     -78.3889     eastus2
West US               37.783      -122.417     westus
North Central US      41.8819     -87.6278     northcentralus
South Central US      29.4167     -98.5        southcentralus
North Europe          53.3478     -6.2597      northeurope
West Europe           52.3667     4.9          westeurope
Japan West            34.6939     135.5022     japanwest
Japan East            35.68       139.77       japaneast
Brazil South          -23.55      -46.633      brazilsouth
Australia East        -33.86      151.2094     australiaeast
Australia Southeast   -37.8136    144.9631     australiasoutheast
South India           12.9822     80.1636      southindia
Central India         18.5822     73.9197      centralindia
West India            19.088      72.868       westindia
Canada Central        43.653      -79.383      canadacentral
Canada East           46.817      -71.217      canadaeast
UK South              50.941      -0.799       uksouth
UK West               53.427      -3.084       ukwest
West Central US       40.890      -110.234     westcentralus
West US 2             47.233      -119.852     westus2
Korea Central         37.5665     126.9780     koreacentral
Korea South           35.1796     129.0756     koreasouth
France Central        46.3772     2.3730       francecentral
France South          43.8345     2.1972       francesouth
Australia Central     -35.3075    149.1244     australiacentral
Australia Central 2   -35.3075    149.1244     australiacentral2
UAE Central           24.466667   54.366669    uaecentral
UAE North             25.266666   55.316666    uaenorth
South Africa North    -25.731340  28.218370    southafricanorth
South Africa West     -34.075691  18.843266    southafricawest
Switzerland North     47.451542   8.564572     switzerlandnorth
Switzerland West      46.204391   6.143158     switzerlandwest
Germany North         53.073635   8.806422     germanynorth
Germany West Central  50.110924   8.682127     germanywestcentral
```
* Prepare deciding the VM size using the `az resource list-locations` command, in this case selecting only 'Standard_B'
```
$ az vm list-sizes --subscription 8e79d269-e904-4c8e-a3d8-5503f0e310e7 --location eastus --query "sort_by([?contains(name,'Standard_B')].{Name:name,Cores:numberOfCores,MB:memoryInMb},&Cores)" --output table
Name            Cores    MB
--------------  -------  -----
Standard_B1ls   1        512
Standard_B1ms   1        2048
Standard_B1s    1        1024
Standard_B2ms   2        8192
Standard_B2s    2        4096
Standard_B4ms   4        16384
Standard_B8ms   8        32768
Standard_B12ms  12       49152
Standard_B16ms  16       65536
Standard_B20ms  20       81920
```
* Prepare deciding the VM IMAGE using the `az vm image list` command, in this case selecting only 'CentOS'
```
$ az vm image list --location eastus --offer centos --output table
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer    Publisher    Sku    Urn                          UrnAlias    Version
-------  -----------  -----  ---------------------------  ----------  ---------
CentOS   OpenLogic    7.5    OpenLogic:CentOS:7.5:latest  CentOS      latest
```
* Prepare deciding the ADMIN account using the `az vm list` command with `--query` option
```
$ az vm list --resource-group rg-eastus-01 --query "[?storageProfile.osDisk.osType=='Linux'].{Name:name,  admin:osProfile.adminUsername}" --output table
Name        Admin
----------  -------
vm-solo-03  bjro
```
***

* Use the `az vm create -h` command to show options
```
$ az vm create -h

Command
    az vm create : Create an Azure Virtual Machine.
        For an end-to-end tutorial, see https://docs.microsoft.com/azure/virtual-machines/virtual-
        machines-linux-quick-create-cli.

Arguments
    --name -n           [Required] : Name of the virtual machine.
    --resource-group -g [Required] : Name of resource group. You can configure the default group
                                     using `az configure --defaults group=<name>`.
    --availability-set             : Name or ID of an existing availability set to add the VM to.
                                     None by default.
    --boot-diagnostics-storage     : Pre-existing storage account name or its blob uri to capture
                                     boot diagnostics. Its sku should be one of Standard_GRS,
                                     Standard_LRS and Standard_RAGRS.
    --computer-name                : The host OS name of the virtual machine. Defaults to the name
                                     of the VM.
    --custom-data                  : Custom init script file or text (cloud-init, cloud-config,
                                     etc..).
    --image                        : The name of the operating system image as a URN alias, URN,
                                     custom image name or ID, or VHD blob URI. This parameter is
                                     required unless using `--attach-os-disk.` Valid URN format:
                                     "Publisher:Offer:Sku:Version".  Values from: az vm image list,
                                     az vm image show.
    --license-type                 : Specifies that the Windows image or disk was licensed on-
                                     premises. To enable Azure Hybrid Benefit for Windows Server,
                                     use 'Windows_Server'. To enable Multitenant Hosting Rights for
                                     Windows 10, use 'Windows_Client'. For more information see the
                                     Azure Windows VM online docs.  Allowed values: None,
                                     Windows_Client, Windows_Server.
    --location -l                  : Location in which to create VM and related resources. If
                                     default location is not configured, will default to the
                                     resource group's location.
    --no-wait                      : Do not wait for the long-running operation to finish.
    --ppg                          : The name or ID of the proximity placement group the VM should
                                     be associated with.
    --secrets                      : One or many Key Vault secrets as JSON strings or files via
                                     `@{path}` containing `[{ "sourceVault": { "id": "value" },
                                     "vaultCertificates": [{ "certificateUrl": "value",
                                     "certificateStore": "cert store name (only on windows)"}] }]`.
    --size                         : The VM size to be created. See
                                     https://azure.microsoft.com/pricing/details/virtual-machines/
                                     for size info.  Default: Standard_DS1_v2.
    --tags                         : Space-separated tags in 'key[=value]' format. Use '' to clear
                                     existing tags.
    --validate                     : Generate and validate the ARM template without creating any
                                     resources.
    --zone -z                      : Availability zone into which to provision the resource.
                                     Allowed values: 1, 2, 3.

Authentication Arguments
    --admin-password               : Password for the VM if authentication type is 'Password'.
    --admin-username               : Username for the VM.  Default: bjro.
    --authentication-type          : Type of authentication to use with the VM. Defaults to password
                                     for Windows and SSH public key for Linux. "all" enables both
                                     ssh and password authentication.  Allowed values: all,
                                     password, ssh.
    --generate-ssh-keys            : Generate SSH public and private key files if missing. The keys
                                     will be stored in the ~/.ssh directory.
    --ssh-dest-key-path            : Destination file path on the VM for the SSH key. If the file
                                     already exists, the specified key(s) are appended to the file.
    --ssh-key-values               : Space-separated list of SSH public keys or public key file
                                     paths.

Dedicated Host Arguments
    --host               [Preview] : Name or ID of the dedicated host this VM will reside
                                     in. If a name is specified, a host group must be specified via
                                     `--host-group`.
        Argument '--host' is in preview. It may be changed/removed in a future release.
    --host-group         [Preview] : Name of the dedicated host group containing the
                                     dedicated host this VM will reside in.
        Argument '--host-group' is in preview. It may be changed/removed in a future
        release.

Managed Service Identity Arguments
    --assign-identity              : Accept system or user assigned identities separated by spaces.
                                     Use '[system]' to refer system assigned identity, or a resource
                                     id to refer user assigned identity. Check out help for more
                                     examples.
    --role                         : Role name or id the system assigned identity will have.
                                     Default: Contributor.
    --scope                        : Scope that the system assigned identity can access.

Marketplace Image Plan Arguments
    --plan-name                    : Plan name.
    --plan-product                 : Plan product.
    --plan-promotion-code          : Plan promotion code.
    --plan-publisher               : Plan publisher.

Network Arguments
    --accelerated-networking       : Enable accelerated networking. Unless specified, CLI will
                                     enable it based on machine image and size.  Allowed values:
                                     false, true.
    --asgs                         : Space-separated list of existing application security groups to
                                     associate with the VM.
    --nics                         : Names or IDs of existing NICs to attach to the VM. The first
                                     NIC will be designated as primary. If omitted, a new NIC will
                                     be created. If an existing NIC is specified, do not specify
                                     subnet, VNet, public IP or NSG.
    --nsg                          : The name to use when creating a new Network Security Group
                                     (default) or referencing an existing one. Can also reference an
                                     existing NSG by ID or specify "" for none.
    --nsg-rule                     : NSG rule to create when creating a new NSG. Defaults to open
                                     ports for allowing RDP on Windows and allowing SSH on Linux.
                                     Allowed values: RDP, SSH.
    --private-ip-address           : Static private IP address (e.g. 10.0.0.5).
    --public-ip-address            : Name of the public IP address when creating one (default) or
                                     referencing an existing one. Can also reference an existing
                                     public IP by ID or specify "" for None.
    --public-ip-address-allocation : Allowed values: dynamic, static.
    --public-ip-address-dns-name   : Globally unique DNS name for a newly created public IP.
    --public-ip-sku                : Sku.  Allowed values: Basic, Standard.
    --subnet                       : The name of the subnet when creating a new VNet or referencing
                                     an existing one. Can also reference an existing subnet by ID.
                                     If omitted, an appropriate VNet and subnet will be selected
                                     automatically, or a new one will be created.
    --subnet-address-prefix        : The subnet IP address prefix to use when creating a new VNet in
                                     CIDR format.  Default: 10.0.0.0/24.
    --vnet-address-prefix          : The IP address prefix to use when creating a new VNet in CIDR
                                     format.  Default: 10.0.0.0/16.
    --vnet-name                    : Name of the virtual network when creating a new one or
                                     referencing an existing one.

Storage Arguments
    --attach-data-disks            : Attach existing data disks to the VM. Can use the name or ID of
                                     a managed disk or the URI to an unmanaged disk VHD.
    --attach-os-disk               : Attach an existing OS disk to the VM. Can use the name or ID of
                                     a managed disk or the URI to an unmanaged disk VHD.
    --data-disk-caching            : Storage caching type for data disk(s), including 'None',
                                     'ReadOnly', 'ReadWrite', etc. Use a singular value to apply on
                                     all disks, or use '<lun>=<vaule1> <lun>=<value2>' to configure
                                     individual disk.
    --data-disk-sizes-gb           : Space-separated empty managed data disk sizes in GB to create.
    --ephemeral-os-disk  [Preview] : Allows you to create an OS disk directly on the host
                                     node, providing local disk performance and faster VM/VMSS
                                     reimage time.  Allowed values: false, true.
        Argument '--ephemeral-os-disk' is in preview. It may be changed/removed in a future
        release.
    --os-disk-caching              : Storage caching type for the VM OS disk. Default: ReadWrite.
                                     Allowed values: None, ReadOnly, ReadWrite.
    --os-disk-name                 : The name of the new VM OS disk.
    --os-disk-size-gb              : The size of the os disk in GB.
    --os-type                      : Type of OS installed on a custom VHD. Do not use when
                                     specifying an URN or URN alias.  Allowed values: linux,
                                     windows.
    --storage-account              : Only applicable when used with `--use-unmanaged-disk`. The name
                                     to use when creating a new storage account or referencing an
                                     existing one. If omitted, an appropriate storage account in the
                                     same resource group and location will be used, or a new one
                                     will be created.
    --storage-container-name       : Only applicable when used with `--use-unmanaged-disk`. Name of
                                     the storage container for the VM OS disk. Default: vhds.
    --storage-sku                  : The SKU of the storage account with which to persist VM. Use a
                                     singular sku that would be applied across all disks, or specify
                                     individual disks. Usage: [--storage-sku SKU | --storage-sku
                                     ID=SKU ID=SKU ID=SKU...], where each ID is "os" or a 0-indexed
                                     lun. Allowed values: Standard_LRS, Premium_LRS,
                                     StandardSSD_LRS, UltraSSD_LRS.
    --ultra-ssd-enabled  [Preview] : Enables or disables the capability to have 1 or more
                                     managed data disks with UltraSSD_LRS storage account.  Allowed
                                     values: false, true.
        Argument '--ultra-ssd-enabled' is in preview. It may be changed/removed in a future
        release.
    --use-unmanaged-disk           : Do not use managed disk to persist VM.

Global Arguments
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, none, table, tsv,
                                     yaml.  Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more
                                     information and examples.
    --subscription                 : Name or ID of subscription. You can configure the default
                                     subscription using `az account set -s NAME_OR_ID`.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.

Examples
    Create a default Ubuntu VM with automatic SSH authentication.
        az vm create -n MyVm -g MyResourceGroup --image UbuntuLTS


    Create a default RedHat VM with automatic SSH authentication using an image URN.
        az vm create -n MyVm -g MyResourceGroup --image RedHat:RHEL:7-RAW:7.4.2018010506


    Create a default Windows Server VM with a private IP address.
        az vm create -n MyVm -g MyResourceGroup --public-ip-address "" --image Win2012R2Datacenter


    Create a VM from a custom managed image.
        az vm create -g MyResourceGroup -n MyVm --image MyImage


    Create a VM by attaching to a managed operating system disk.
        az vm create -g MyResourceGroup -n MyVm --attach-os-disk MyOsDisk --os-type linux


    Create an Ubuntu Linux VM using a cloud-init script for configuration. See:
    https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-using-cloud-init.
        az vm create -g MyResourceGroup -n MyVm --image debian --custom-data MyCloudInitScript.yml


    Create a Debian VM with SSH key authentication and a public DNS entry, located on an existing
    virtual network and availability set.
        az vm create -n MyVm -g MyResourceGroup --image debian --vnet-name MyVnet --subnet subnet1 \
            --availability-set MyAvailabilitySet --public-ip-address-dns-name MyUniqueDnsName \
            --ssh-key-value @key-file


    Create a simple Ubuntu Linux VM with a public IP address, DNS entry, two data disks (10GB and
    20GB), and then generate ssh key pairs.
        az vm create -n MyVm -g MyResourceGroup --public-ip-address-dns-name MyUniqueDnsName \
            --image ubuntults --data-disk-sizes-gb 10 20 --size Standard_DS2_v2 \
            --generate-ssh-keys


    Create a Debian VM using Key Vault secrets.
        az keyvault certificate create --vault-name vaultname -n cert1 \
          -p "$(az keyvault certificate get-default-policy)"

        secrets=$(az keyvault secret list-versions --vault-name vaultname \
          -n cert1 --query "[?attributes.enabled].id" -o tsv)

        vm_secrets=$(az vm secret format -s "$secrets")

        az vm create -g group-name -n vm-name --admin-username deploy  \
          --image debian --secrets "$vm_secrets"


    Create a CentOS VM with a system assigned identity. The VM will have a 'Contributor' role with
    access to a storage account.
        az vm create -n MyVm -g rg1 --image centos --assign-identity --scope
        /subscriptions/99999999-1bf0-4dda-
        aec3-cb9272f09590/MyResourceGroup/myRG/providers/Microsoft.Storage/storageAccounts/storage1


    Create a debian VM with a user assigned identity.
        az vm create -n MyVm -g rg1 --image debian --assign-identity
        /subscriptions/99999999-1bf0-4dda-aec3-cb9272f09590/resourcegroups/myRG/providers/Microsoft.
        ManagedIdentity/userAssignedIdentities/myID


    Create a debian VM with both system and user assigned identity.
        az vm create -n MyVm -g rg1 --image debian --assign-identity  [system]
        /subscriptions/99999999-1bf0-4dda-aec3-cb9272f09590/resourcegroups/myRG/providers/Microsoft.
        ManagedIdentity/userAssignedIdentities/myID


    Create a VM in an availability zone in the current resource group's region
        az vm create -n MyVm -g MyResourceGroup --image Centos --zone 1
```
