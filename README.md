# Work with DNS

One of the first client of your application is a company, called 'ornottodo' - they want to deploy your todo app into their internal network (which is currently in Azure). The challenge here - 'ornottodo' folks want to have it available by the domain name rather than by internal IP, so in this task you will have to learn how to work with DNS in Azure.  

## How to complete tasks in this module 

Tasks in this module are relying on 2 PowerShell scripts: 

- `scripts/generate-artifacts.ps1` generates the task "artifacts" and uploads them to cloud storage. An "artifact" is evidence of a task completed by you. Each task will have its own script, which will gather the required artifacts. The script also adds a link to the generated artifact in the `artifacts.json` file in this repository — make sure to commit changes to this file after you run the script. 
- `scripts/validate-artifacts.ps1` validates the artifacts generated by the first script. It loads information about the task artifacts from the `artifacts.json` file.

Here is how to complete tasks in this module:

1. Clone task repository

2. Make sure you completed steps, described in the Prerequisites section

3. Complete the task, described in the Requirements section 

4. Run `scripts/generate-artifacts.ps1` to generate task artifacts. Script will update the file `artifacts.json` in this repo. 

5. Run `scripts/validate-artifacts.ps1` to test yourself. If tests are failing - follow the recomendation from the test script error message to fix or re-deploy your infrastructure. When you will be ready to test yourself again - **re-generate the artifacts** (step 4) and re-run tests again. 

6. When all tests will pass - commit your changes and submit the solution for a review. 

Pro tip: if you stuck with any of the implementation steps - run `scripts/generate-artifacts.ps1` and `scripts/validate-artifacts.ps1`. The validation script might give you a hint on what you should do.  

## Prerequisites

Before completing any task in the module, make sure that you followed all the steps described in the **Environment Setup** topic, in particular: 

1. Ensure you have an [Azure](https://azure.microsoft.com/en-us/free/) account and subscription.

2. Create a resource group called *"mate-resources"* in the Azure subscription.

3. In the *"mate-resources"* resource group, create a storage account (any name) and a *"task-artifacts"* container.

4. Install [PowerShell 7](https://learn.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7.4) on your computer. All tasks in this module use Powershell 7. To run it in the terminal, execute the following command: 
    ```
    pwsh
    ```

5. Install [Azure module for PowerShell 7](https://learn.microsoft.com/en-us/powershell/azure/install-azure-powershell?view=azps-11.3.0): 
    ```
    Install-Module -Name Az -Repository PSGallery -Force
    ```
If you are a Windows user, before running this command, please also run the following: 
    ```
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
    ```

6. Log in to your Azure account using PowerShell:
    ```
    Connect-AzAccount -TenantId <your Microsoft Entra ID tenant id>
    ```

## Requirements

In this task, you will need to update existing PowerShell script `task.ps1`. The original script deploys 2 virtual machines: 

- `webserver`, used to host the todo web app. It's deployed in a subnet `web`, without public IP.
- `jumpbox`, used as a dedicated virtual machine for managemen of `webserver`. It is deployed to separate subnet, has public IP, and allows connecting to it through SSH.  

In this task, you need to deploy a private DNS zone `todo.ornottodo`, create a DNS record in it: 

1. Review the script `task.ps1`.  

2. Add deployment of private DNS zone `or.nottodo`. To deploy a private DNS zone, use comandlet [New-AzPrivateDnsZone](https://learn.microsoft.com/en-us/powershell/module/az.privatedns/new-azprivatednszone?view=azps-12.0.0). 

3. Don't forget to add linking of newly-created DNS zone to the virtual network, where VMs are deployed - you can do it with comandlet [New-AzPrivateDnsVirtualNetworkLink](https://learn.microsoft.com/en-us/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink?view=azps-12.0.0). Make sure to enable [auto-registration](https://learn.microsoft.com/en-us/azure/dns/private-dns-autoregistration) feature when creating a link. 

4. Add creating of a CNAME record in the private DNS zone - for that use comandlet [New-AzPrivateDnsRecordSet](https://learn.microsoft.com/en-us/powershell/module/az.privatedns/new-azprivatednsrecordset?view=azps-12.0.0). CNAME should point domain `todo.or.nottodo` to the domain, `webserver` virtual machine will get with auto-registration in the private DNS zone. 

5. When script is ready, run it to deploy resources to your subcription.

6. To test yourself, connect to the `jumbox` VM with SSH, and test with curl, if you can get to the todo web app by it's domain name `todo.or.nottodo` (app will be running on port 8080, so you will need to use the following URL for curl: "http://todo.or.nottodo:8080/"). 

7. Run artifacts generation script `scripts/generate-artifacts.ps1`.

8. Test yourself using the script `scripts/validate-artifacts.ps1`.

9. Make sure that changes to both `task.ps1` and `result.json` are commited to the repo, and sumbit the solution for a review. 

10. When solution is validated, delete resources you deployed with the powershell script - you won't need them for the next tasks. 
