# Azure CLI #

`az interactive` - gives you a easy way for CLI to auto-complete your typing 

# VM Creation 

- Grabbing the learn* resource group and storing in a var for use: `rg_learn=$(az group list --query "[].[name]" -o tsv | grep learn*)`

```
az vm create \
  --resource-group $rg_learn \
  --name my-vm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

```
az vm extension set \
  --resource-group $rg_learn \
  --vm-name my-vm \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --version 2.1 \
  --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}' \
  --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'
```

# SAS Install 
> in the destination file, find the regexp and if there, replace with the line value - brilliance of Erwan 
```
ansible localhost -m lineinfile \
  -a "dest=~/.bashrc \
      regexp='^export STUDENT' \
      line='export STUDENT=malepr'" --diff
```
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
- after you create the service principal using az ad sp create-for-rbac - seems that GEL SKIPPED ASSIGNMENT so that they could get the password in one command, store it and the ID in another. 
- BUUUT that still leaves you with a service principal with no role - so you assign it later as a contributor 
- HOWEVER - to do that, you need to be an owner or user admin in Azure to write roles to a particular subscription 
- This also means - it is okay to create a service principal that is different from your student name 
az role assignment create --assignee $SP_APPID --role Contributor --scopes="/subscriptions/"

- validate with: 
az login --service-principal -u $TF_VAR_client_id -p $TF_VAR_client_secret --tenant $TF_VAR_tenant_id
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