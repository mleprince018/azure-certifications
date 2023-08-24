# Azure CLI #
[Main Azure Doc Page](https://learn.microsoft.com/en-us/cli/azure/)

`az interactive` - gives you a easy way for CLI to auto-complete your typing 

# VM Creation 

- Grabbing the learn* resource group and storing in a var for use: `rg_learn=$(az group list --query "[].[name]" -o tsv | grep learn*)`

```bash
az vm create \
  --resource-group $rg_learn \
  --name my-vm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys

az vm extension set \
  --resource-group $rg_learn \
  --vm-name my-vm \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --version 2.1 \
  --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}' \
  --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'
```

```
marc [ ~ ]$ az vm list-ip-addresses
[
  {
    "virtualMachine": {
      "name": "my-vm",
      "network": {
        "privateIpAddresses": [
          "10.0.0.4"
        ],
        "publicIpAddresses": [
          {
            "id": "/subscriptions/76b50b69-6a20-4079-b075-3f796c72cac3/resourceGroups/learn-6f1d149f-0c34-4ad2-ad0d-398c213cc66f/providers/Microsoft.Network/publicIPAddresses/my-vmPublicIP",
            "ipAddress": "20.253.162.210",
            "ipAllocationMethod": "Dynamic",
            "name": "my-vmPublicIP",
            "resourceGroup": "learn-6f1d149f-0c34-4ad2-ad0d-398c213cc66f",
            "zone": null
          }
        ]
      },
      "resourceGroup": "learn-6f1d149f-0c34-4ad2-ad0d-398c213cc66f"
    }
  }
]
IPADDRESS="$(az vm list-ip-addresses \
  --resource-group $rg_learn \
  --name my-vm \
  --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress" \
  --output tsv)"

az network nsg list \
  --resource-group $rg_learn \
  --query '[].name' \
  --output tsv
```
> Adding a network NSG rule to allow HTTP traffic to the VM 
```bash 
marc [ ~ ]$ az network nsg rule list   --resource-group $rg_learn   --nsg-name my-vmNSG
```
```json
[
  {
    "access": "Allow",
    "destinationAddressPrefix": "*",
    "destinationAddressPrefixes": [],
    "destinationPortRange": "22",
    "destinationPortRanges": [],
    "direction": "Inbound",
    "etag": "W/\"2875972e-133e-41a2-9145-a9c604ed1dd7\"",
    "id": "/subscriptions/76b50b69-6a20-4079-b075-3f796c72cac3/resourceGroups/learn-6f1d149f-0c34-4ad2-ad0d-398c213cc66f/providers/Microsoft.Network/networkSecurityGroups/my-vmNSG/securityRules/default-allow-ssh",
    "name": "default-allow-ssh",
    "priority": 1000,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "learn-6f1d149f-0c34-4ad2-ad0d-398c213cc66f",
    "sourceAddressPrefix": "*",
    "sourceAddressPrefixes": [],
    "sourcePortRange": "*",
    "sourcePortRanges": [],
    "type": "Microsoft.Network/networkSecurityGroups/securityRules"
  }
]
```

```bash
az network nsg rule list \
  --resource-group $rg_learn \
  --nsg-name my-vmNSG \
  --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' \
  --output table
Name               Priority    Port    Access
-----------------  ----------  ------  --------
default-allow-ssh  1000        22      Allow

az network nsg rule create \
  --resource-group $rg_learn \
  --nsg-name my-vmNSG \
  --name allow-http \
  --protocol tcp \
  --priority 100 \
  --destination-port-range 80 \
  --access Allow

az network nsg rule list \
  --resource-group $rg_learn \
  --nsg-name my-vmNSG \
  --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' \
  --output table
Name               Priority    Port    Access
-----------------  ----------  ------  --------
default-allow-ssh  1000        22      Allow
allow-http         100         80      Allow
```

 - use the '.' to traverse down the list to indv child The [*] allows you to pick up all the children for a particular thing

# Helpful Tips 

- Use below to grab the cloudshell IP address 
`curl -s checkip.dyndns.org | sed -e 's/.*Current IP Address: //' -e 's/<.*$//'`

# SAS Install 
> in the destination file, find the regexp and if there, replace with the line value - brilliance of Erwan 
```
ansible localhost -m lineinfile \
  -a "dest=~/.bashrc \
      regexp='^export STUDENT' \
      line='export STUDENT=malepr'" --diff
ansible localhost -m lineinfile -a "dest=~/.bashrc \
      regexp='^export TF_VAR_client_secret' \
      line='export TF_VAR_client_secret=$(az ad sp create-for-rbac --skip-assignment --name http://${STUDENT} --query password --output tsv)'" --diff
ansible localhost -m lineinfile -a "dest=~/.bashrc \
      regexp='^export TF_VAR_client_id' \
      line='export TF_VAR_client_id=$(az ad sp show --id http://${STUDENT} --query appId -o tsv)'" --diff
ansible localhost -m lineinfile -a "dest=~/.bashrc \
      regexp='^export TF_VAR_subscription_id' \
      line='export TF_VAR_subscription_id=$(az account list --query "[?name=='TeamFD'].{id:id}" -o tsv)'" --diff
ansible localhost -m lineinfile -a "dest=~/.bashrc \
      regexp='^export TF_VAR_tenant_id' \
      line='export TF_VAR_tenant_id=$(az account list --query "[?name=='TeamFD'].{tenantId:tenantId}" -o tsv)'" --diff
```
- after you create the service principal using az ad sp create-for-rbac - seems that GEL SKIPPED ASSIGNMENT so that they could get the password in one command, store it and the ID in another. 
- BUUUT that still leaves you with a service principal with no role - so you assign it later as a contributor 
- HOWEVER - to do that, you need to be an owner or user admin in Azure to write roles to a particular subscription 
- This also means - it is okay to create a service principal that is different from your student name 
az role assignment create --assignee $SP_APPID --role Contributor --scopes="/subscriptions/"

- validate with: `az login --service-principal -u $TF_VAR_client_id -p $TF_VAR_client_secret --tenant $TF_VAR_tenant_id`
- if terraform doesn't init - https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/guides/service_principal_client_secret 
- *somehow GEL team didn't use a service principal?* 


Error: Error Creating/Updating Network Security Rule "malepr-viya4-ssh" (NSG "malepr-viya4-nsg" / Resource Group "malepr-viya4-rg"): 
    network.SecurityRulesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- 
    Original Error: Code="OverlappingSubnetsNotPermittedInSecurityRule" 
    Message="Security rule parameter SourceAddressPrefix for rule with Id 
    /subscriptions/fd027923-0ba6-4fb3-8d64-623608ea2a44/resourceGroups/malepr-viya4-rg/providers/Microsoft.Network/networkSecurityGroups/malepr-viya4-nsg/securityRules/malepr-viya4-ssh 
    contains overlapping subnets [149.173.0.0/16, 149.173.8.27/32], which is not permitted." Details=[]
- Default cidrs of GEL had 2 that overlapped. Corrected them in variables and all was well. 

# References: 
- MSFT Docs | [Tips for CLI](https://learn.microsoft.com/en-us/cli/azure/use-cli-effectively?tabs=bash%2Cbash2)
- https://techcommunity.microsoft.com/t5/itops-talk-blog/how-to-query-azure-resources-using-the-azure-cli/ba-p/360147 