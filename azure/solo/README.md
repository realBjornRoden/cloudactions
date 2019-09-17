# Azure Create SOLO CentOS Linux Virtual Machine (VM)
* The Azure Command-Line Interface (CLI) | Microsoft Docs
[azure-cli-lates](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest)
## Actions
### Login
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
$ az vm show --show-details --resource-group rg-eastus-01 --name vm-solo-03 --output table
Name        ResourceGroup    PowerState    PublicIps      Fqdns    Location    Zones
----------  ---------------  ------------  -------------  -------  ----------  -------
vm-solo-03  rg-eastus-01     VM running    13.68.223.143           eastus

```

