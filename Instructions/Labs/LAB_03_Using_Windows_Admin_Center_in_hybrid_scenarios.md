---
lab:
    title: 'Lab: Using Windows Admin Center in hybrid scenarios'
    module: 'Module 3: Facilitating hybrid management and operational monitoring in hybrid scenarios'
---

# Lab: Using Windows Admin Center in hybrid scenarios

## Scenario

To address concerns regarding consistent operational and management model, regardless of the location of managed systems, you'll test the capabilities of Windows Admin Center in the hybrid environment containing different versions of Windows Server operating system running on-premises and in Azure VMs.

Your goal is to verify that Windows Admin Center can be used in a consistent manner regardless of the location of managed systems.

## Objectives

After completing this lab, you'll be able to:

- Test hybrid connectivity by using Azure Network Adapter.
- Deploy Windows Admin Center gateway in Azure.
- Verify functionality of Windows Admin Center gateway in Azure.

## Estimated time: 90 minutes

## Lab setup

Lab virtual machines: **SEA-CL1**, **SEA-DC1**, and **SEA-SVR2**

User name: **CONTOSO\\Administrator**
Password: **Pa55w.rd**

For this lab, you'll use SEA-CL1, SEA-DC1, and SEA-SVR2 lab virtual machines and an Azure subscription. Before you begin the lab, complete the following steps:

1. Ensure that you have an Azure subscription and a user account with the Owner or Contributor role in that subscription.
1. Start SEA-CL1, SEA-DC1 and SEA-SVR2.

## Exercise 1: Provisioning Azure VMs running Windows Server 2019

### Scenario

You need to verify that you can establish hybrid connectivity between an on-premises server and an Azure virtual network. To start, you'll provision Azure VMs running Windows Server 2019 by using an ARM template.

The main tasks for this exercise are as follows:

1. Create an Azure resource group by using an Azure Resource Manager template.
1. Create an Azure VM by using an Azure Resource Manager template.

### Task 1: Create an Azure resource group by using an Azure Resource Manager template

1. On SEA-CL1, start Microsoft Edge, navigate to the [Azure portal](https://portal.azure.com), and sign in by using credentials of a user account with the Owner role in the subscription you'll be using in this lab.
1. In the Azure portal, open a PowerShell session in the **Cloud Shell** blade.
1. Upload the file **M03-lab-sub_template.json** , which can be found at C:\Labfiles\Mod03 into the Cloud Shell home directory.
1. From the Cloud Shell blade, run the following to create a resource group that will contain resources you provision in this lab (replace the `<Azure region>` placeholder with the name of the Azure region that is available in your subscription and which is closest to the location of your lab computer):

   ```pwsh
   $location = '<Azure region>'
   New-AzSubscriptionDeployment `
     -Location $location `
     -Name ws2019-m031subDeployment `
     -TemplateFile $HOME/M03-lab-sub_template.json `
     -rgLocation $location `
     -rgName 'ws2019-m031-rg'
   ```

    >**Note**: To identify Azure regions where you can provision Azure VMs, refer to [Find Azure credit offers in your region](https://aka.ms/regions-offers).

### Task 2: Create an Azure VM by using an Azure Resource Manager template

1. From the Cloud Shell blade, upload an Azure Resource Manager template **M03-lab-rg_template.json** and a parameter file **M03-lab-rg_template.parameters.json**.

1. From the Cloud Shell blade, run the following to deploy a Azure VM running Windows Server 2019 that you'll be using in this lab:

   ```pwsh
   New-AzResourceGroupDeployment `
     -Name ws2019-m031rgDeployment `
     -ResourceGroupName 'ws2019-m031-rg' `
     -TemplateFile $HOME/M03-lab-rg_template.json `
     -TemplateParameterFile $HOME/M03-lab-rg_template.parameters.json
   ```

    >**Note**: Wait for the deployment to complete before you proceed to the next exercise. The deployment should take less than 5 minutes.
    
1. Review the ws2019-m03-vnet subnets. If there is no gateway subnet, then create a Gateway subnet using 10.3.3.224/27.

## Exercise 2: Implementing hybrid connectivity by using the Azure Network Adapter

### Scenario

You need to verify that you can establish hybrid connectivity between an on-premises server and the Azure VM you provisioned in the previous exercise. You'll use for this purpose the Azure Network Adapter feature of Windows Admin Center.

The main tasks for this exercise are as follows:

1. Register Windows Admin Center with Azure.
1. Create an Azure Network Adapter.

### Task 1: Register Windows Admin Center with Azure

1. On SEA-CL1, start Microsoft Edge and connect to Windows Admin Center running on SEA-SVR2.
1. From the Windows Admin Center page, attempt to add an Azure Network Adapter.
1. When prompted, register Windows Admin Center to the Azure subscription you used in the previous exercise. On the **Get started with Azure in Windows Admin Center** page for the **Azure Active Directory application** prompt, select **Create New**.  Complete the remaining steps.

### Task 2: Create an Azure Network Adapter

1. On SEA-CL1, in the Microsoft Edge window displaying Windows Admin Center running on SEA-SVR2 attempt to create an Azure Network Adapter again.
1. Create an Azure Network Adapter with the following settings:

*Table 1: Azure Network Adapter settings*

|Setting| Value|
|---|---|
|Subscription|the name of the Azure subscription you are using in this lab|
|Location|the name of the Azure region into which you deployed the Azure VMs in the previous exercise|
|Virtual network|ws2019-m03-vnet|
|Gateway subnet|10.3.3.224/27|
|Gateway SKU|VpnGw1|
|Client Address Space|192.168.0.0/24|
|Authentication Certificate|Auto-generated Self-signed root and client Certificate|

3. On SEA-CL1, switch to the Microsoft Edge window displaying the Azure portal and verify that a new virtual network gateway with the name starting with **WAC-Created-vpngw-** is being provisioned.

4. On SEA-CL1, in the Microsoft Edge window displaying the Windows Admin Center running on SEA-SVR2, verify that you see a new network adapter named **WACVPN-26123** representing the Point-to-Site VPN connection with the IPv4 address of **192.168.0.1**.

>**Note**: The provisioning of the Azure virtual network gateway can take up to 45 minutes. Do not wait for the provisioning to complete but instead proceed to the next exercise.

## Exercise 3: Deploying Windows Admin Center gateway in Azure

### Scenario

You need to evaluate the ability to manage Azure VMs running Windows Server OS by using Windows Admin Center. To accomplish this, you'll first install a Windows Admin Center gateway in the Azure virtual network you implemented in the first exercise of this lab.

The main tasks for this exercise are as follows:

1. Install Windows Admin Center gateway in Azure.
1. Review results of the script provisioning.

### Task 1: Install Windows Admin Center gateway in Azure

1. On SEA-CL1, switch to the browser window displaying the the Azure portal.
1. Back in the Azure portal, start a PowerShell session in the **Azure Cloud Shell** blade.
1. From the Cloud Shell blade, upload the file **Deploy-WACAzVM.ps1** into the Cloud Shell home directory.
1. From the Cloud Shell blade, run the following to enable the compatibility for the **AzureRm** PowerShell cmdlets that are used by the Windows Admin Center provisioning script:

   ```pwsh
   Enable-AzureRmAlias -Scope Process
   ```

1. From the Cloud Shell blade, run the following to set values of variables necessary to run the the Windows Admin Center provisioning script:

   ```pwsh
   $rgName = 'ws2019-m031-rg'
   $vnetName = 'ws2019-m03-vnet'
   $nsgName = 'ws2019-m03-web-nsg'
   $subnetName = 'subnet1'
   $location = 'eastus'
   $pipName = 'wac-public-ip'
   $size = 'Standard_D2s_v3'
   $image = 'Win2019Datacenter'
   ```

1. From the Cloud Shell blade, run the following to set the script parameters variable:

   ```pwsh
   $scriptParams = @{
     ResourceGroupName = $rgName
     Name = 'ws2019-wac-vm'
     VirtualNetworkName = $vnetName
     SubnetName = $subnetName
     GenerateSslCert = $true
   }
   ```

1. From the Cloud Shell blade, run the following to launch the provisioning script:

   ```pwsh
   ./Deploy-WACAzVM.ps1 @scriptParams
   ```

1. When prompted to provide the name for the local Administrator account, enter **Student**
1. When prompted to provide the password for the local Administrator account, enter **Pa55w.rd1234**

    >**Note**: Wait for the provisioning script to complete. This might take about 5 minutes.

1. Verify that the script completed successfully and note the final message providing the URL for the connection to the Windows Admin Center.

### Task 2: Review results of the script provisioning

1. In the Azure portal, navigate to the blade of the **ws2019-m031-rg** resource group.
1. On the **ws2019-m031-rg** blade, on the **Overview** blade, review the list of resources, including the Azure VM **ws2019-wac-vm**.
1. On the **ws2019-wac-vm | Networking** blade, on the **Inbound port rules** tab, note entries representing the inbound port rule allowing connectivity on TCP port 5986 and the inbound rule allowing connectivity on TCP port 443.

## Exercise 4: Verifying functionality of the Windows Admin Center gateway in Azure

### Scenario

With all required components in place, you'll test the WAC functionality targeting the Azure VMs deployed into the Azure virtual network you provisioned in the first exercise of this lab.

The main tasks for this exercise are as follows:

1. Connect to the Windows Admin Center gateway running in Azure VM.
1. Enable PowerShell Remoting on an Azure VM.
1. Connect to an Azure VM by using the Windows Admin Center gateway running in Azure VM.

### Task 1: Connect to the Windows Admin Center gateway running in Azure VM

1. On SEA-CL1, start Microsoft Edge and connect the Windows Admin Center gateway running in the Azure VM you identified in the previous exercise.
1. When prompted, authenticate by using the following credentials:

*Table 2: Sign in credentials*

|Setting|Value|
|---|---|
|Username|Student|
|Password|Pa55w.rd1234|

3. On the **All connections** blade of the Windows Admin Center page, select **ws2019-wac-vm [Gateway]**.

4. When prompted, authenticate by using the following credentials:

*Table 3: Sign in credentials*

|Setting|Value|
|---|---|
|Username|Student|
|Password|Pa55w.rd1234|

5. Examine the **Overview** blade of the Windows Admin Center page.

### Task 2: Enable PowerShell Remoting on an Azure VM

1. On SEA-CL1, in the Microsoft Edge window displaying the Azure portal, navigate to the blade of the **ws2019-m03-vm0** Azure VM.
1. On the **ws2019-m03-vm0** blade, use the **Run Command** feature to run the following command:

   ```pwsh
   winrm quickconfig -quiet
   ```

1. On the **ws2019-m03-vm0** blade, use the **Run Command** feature to run the following command:

   ```pwsh
   Set-NetFirewallRule -Name WINRM-HTTP-In-TCP-PUBLIC -RemoteAddress Any
   ```

1. On the **ws2019-m03-vm0** blade, use the **Run Command** feature to run the following command:

   ```pwsh
   Enable-PSRemoting -Force -SkipNetworkProfileCheck
   ```

### Task 3: Connect to an Azure VM by using the Windows Admin Center gateway running in Azure VM

1. On SEA-CL1, in the Microsoft Edge window displaying the interface of the Windows Admin Center gateway running on the **ws2019-wac-vm** Azure VM, add a connection to the Azure VM **ws2019-m03-vm0** Azure VM by using its IP address.
1. When prompted, authenticate by using the following credentials:

*Table 4: Sign in credentials*

|Setting|Value|
|---|---|
|Username|Student|
|Password|Pa55w.rd1234|

3. Once successfully connected, examine the **Overview** blade of the Windows Admin Center page on the **ws2019-wac-vm** Azure VM

## Exercise 5: Deprovisioning the Azure environment

### Scenario

To minimize Azure-related charges, you'll deprovision the Azure resources provisioned throughout this lab.

The main tasks for this exercise are as follows:

1. Start a PowerShell session in Cloud Shell.
1. Identify all Azure resources provisioned in the lab.

### Task 1: Start a PowerShell session in Cloud Shell

1. On SEA-CL1, switch to the Microsoft Edge window displaying the Azure portal.
1. From the Azure portal, open a PowerShell session in **Azure Cloud Shell** blade.

### Task 2: Identify all Azure resources provisioned in the lab

1. From the Cloud Shell blade, run the following to list all resource groups created throughout this lab:

   ```pwsh
   Get-AzResourceGroup -Name 'ws2019-03-m03'
   ```

1. From the Cloud Shell blade, run the following to delete all resource groups you created throughout this lab:

   ```pwsh
   Get-AzResourceGroup -Name 'ws2019-03-m03' | Remove-AzResourceGroup -Force -AsJob
   ```

### Results

After completing this lab, you have deployed and configured Azure VMs running Windows Server 2019 in the manner that satisfies the Contoso's manageability and security requirements.

### Prepare for the next module

End the lab when you're finished in preparation for the next module.
