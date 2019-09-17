# Create SOLO CentOS Linux Virtual Machine (VM)
* The Azure Command-Line Interface (CLI) | Microsoft Docs
[azure-cli-lates](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest)
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
  "id": "/subscriptions/8e79d269-e904-4c8e-a3d8-5503f0e310e7/resourceGroups/rg-eastus-01",
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
1. Use the `az vm create` command to create a VM
```
$ time az vm create --resource-group rg-eastus-01 --name vm-solo-03 --image CentOS --admin-username bjro --generate-ssh-keys
{
  "fqdns": "",
  "id": "/subscriptions/8e79d269-e904-4c8e-a3d8-5503f0e310e7/resourceGroups/rg-eastus-01/providers/Microsoft.Compute/virtualMachines/vm-solo-03",
  "location": "eastus",
  "macAddress": "00-0D-3A-1E-D2-CB",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.68.223.143",
  "resourceGroup": "rg-eastus-01",
  "zones": ""
}
real	2m16.068s
user	0m1.494s
sys	0m0.208s
```
1. Use the `az vm show --show-details` command to display IP-address of the VM
```
$ az vm show --show-details --resource-group rg-eastus-01 --name vm-solo-03 --output table
Name        ResourceGroup    PowerState    PublicIps      Fqdns    Location    Zones
----------  ---------------  ------------  -------------  -------  ----------  -------
vm-solo-03  rg-eastus-01     VM running    13.68.223.143           eastus

```
1. Use the `az vm create -h` command to show options
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
