---
lab:
    title: 'Lab: Using Windows Admin Center in hybrid scenarios'
    type: 'Answer Key'
    module: 'Module 3: Facilitating hybrid management and operational monitoring in hybrid scenarios'
---

# Lab answer key: Using Windows Admin Center in hybrid scenarios

## Exercise 1: Provisioning Azure VMs running Windows Server 2019

### Task 1: Create an Azure resource group by using an Azure Resource Manager template

1. On SEA-CL1, start Microsoft Edge, navigate to the [Azure portal](https://portal.azure.com), and sign in by using credentials of a user account with the Owner role in the subscription you'll be using in this lab.
1. In the Azure portal, open **Cloud Shell** blade by selecting on the toolbar icon directly next to the search textbox.
1. If prompted to select either **Bash** or **PowerShell**, select **PowerShell**.

    >**Note**: If this is the first time you are starting **Cloud Shell** and you are presented with the **You have no storage mounted** message, select the subscription you are using in this lab, and select **Create storage**.
1. In the toolbar of the Cloud Shell blade, select the **Upload/Download files** icon, in the drop-down menu select **Upload**, and upload the file **M03-lab-sub_template.json** into the Cloud Shell home directory, which can be found at C:\Labfiles\Mod03 into the Cloud Shell home directory.
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

1. From the Cloud Shell blade, upload an Azure Resource Manager template **M03-lab-rg_template.json**, which can be found at C:\Labfiles\Mod03.
1. From the Cloud Shell blade, upload an Azure Resource Manager parameter file **M03-lab-rg_template.parameters.json**.
1. From the Cloud Shell blade, run the following to deploy a Azure VM running Windows Server 2019 that you'll be using in this lab:

   ```pwsh
   New-AzResourceGroupDeployment `
     -Name ws2019-m031rgDeployment `
     -ResourceGroupName 'ws2019-m031-rg' `
     -TemplateFile $HOME/M03-lab-rg_template.json `
     -TemplateParameterFile $HOME/M03-lab-rg_template.parameters.json
   ```

    >**Note**: Wait for the deployment to complete before you proceed to the next exercise. The deployment should take less than 5 minutes.

1. In the Azure portal, close the **Cloud Shell** blade.

1. Review the **ws2019-m03-vnet** subnets. If there is no gateway subnet, then create a **Gateway subnet** using **10.3.3.224/27**.

## Exercise 2: Implementing hybrid connectivity by using the Azure Network Adapter

### Task 1: Register Windows Admin Center with Azure

1. On SEA-CL1, start Microsoft Edge and navigate to ```https://sea-svr2.contoso.com``` in order to connect to Windows Admin Center running on SEA-SVR2.
1. If prompted, sign in as **CONTOSO\\Student** with **Pa55w.rd1234** as the password.
1. On the ```https://sea-svr2.contoso.com``` page in the Microsoft Edge, select the ```sea-srv2.contoso.com [Gateway]``` link.
1. On the Windows Admin Center page, select **Networks**, select **Actions**, and then select **+ Add Azure Network Adapter (Preview)**
1. When prompted, in the **Add Azure Network Adapter** window, select **Register Windows Admin Center to Azure**.

    >**Note**: This will automatically display the **Azure** blade on the **Settings** page within the Windows Admin Center window.

1. On the **Azure** blade on the **Settings** page within the Windows Admin Center window, select **Register**.
1. On the **Get started with Azure in Windows Admin Center** blade, select **Copy** to copy the code displayed in step 1 of the registration procedure.
1. In step 2 of the registration procedure, select the link **Enter the code**.

    >**Note**: This will open another tab in the Microsoft Edge window displaying the **Enter code** page.

1. In the **Enter code** text box, paste the code you copied into Clipboard and select **Next**.
1. On the **Sign in** page, provide the same user name that you used to sign into your Azure subscription in the previous exercise, select **Next**, provide the corresponding password, and select **Sign in**.
1. On the **Windows Admin Center** page verify that the sign in was successful and close the newly opened tab of the Microsoft Edge window.

1. Back on the **Get started with Azure in Windows Admin Center** blade, ensure that **Azure Active Directory application** is set to **Create new** and select **Connect**.
1. On the **Get started with Azure in Windows Admin Center** blade, in step 4, select the link **App Permissions in the Azure portal**.

    >**Note**: This will open another tab in the Microsoft Edge window displaying the API permissions page of the **Windows Admin Center** app newly created in the Azure Active Directory tenant associated with your Azure subscription.

1. On the page of the **Windows Admin Center** app newly created in the Azure Active Directory tenant associated with your Azure subscription, select **Grant admin consent for Default Directory** and, when prompted for confirmation, select **Yes**.
1. Switch back to the Microsoft Edge tab displaying the **Get started with Azure in Windows Admin Center** blade and, in step 5 of the registration procedure, select the **Sign in** link.

### Task 2: Create an Azure Network Adapter

1. In the Microsoft Edge tab, navigate back to the ```sea-srv2.contoso.com``` page and select **Networks** again.
1. On the Windows Admin Center page, in the **Networks** blade, select **+ Add Azure Network Adapter (Preview)**.
1. On the Add Network Adapter Settings blade, specify the following settings and select **Create** (leave others with their default values)"

*Table 1: Azure Network Adapter settings*

|Setting|Value|
|---|---|
|Subscription|The name of the Azure subscription you are using in this lab|
|Location|The name of the Azure region into which you deployed the Azure VMs in the previous exercise|
|Virtual network|ws2019-m03-vnet|
|Gateway subnet|10.3.3.224/27|
|Gateway SKU|VpnGw1|
|Client Address Space|192.168.0.0/24|
|Authentication Certificate|Auto-generated Self-signed root and client Certificate|

4. On SEA-CL1, in the Microsoft Edge window displaying the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Virtual network gateways**.

5. On the **Virtual network gateways** blade, select **Refresh** and verify that the new entry with the name starting with **WAC-Created-vpngw-** appears in the list of virtual network gateways.

6. On SEA-CL1, in the Microsoft Edge window displaying the Windows Admin Center running on SEA-SVR2, refresh the **Networks** blade and confirm that it displays the **WACVPN-26123** entry representing the Point-to-Site VPN connection with the IPv4 address of **192.168.0.1**.

>**Note**: The provisioning of the Azure virtual network gateway can take up to 45 minutes. Do not wait for the provisioning to complete but instead proceed to the next exercise.

## Exercise 3: Deploying Windows Admin Center gateway in Azure

### Task 1: Install Windows Admin Center gateway in Azure

1. On SEA-CL1, switch to the browser window displaying the the Azure portal.
1. Back in the Azure portal, open the **Azure Cloud Shell** blade by selecting by selecting the Cloud Shell button in the Azure Portal.
1. In the toolbar of the Cloud Shell blade, select the **Upload/Download files** icon, in the drop-down menu select **Upload**, and upload the file **Deploy-WACAzVM.ps1** into the Cloud Shell home directory.
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

1. When prompted to provide the name for the local Administrator account, type **Student**
1. When prompted to provide the password for the local Administrator account, type **Pa55w.rd1234**

    >**Note**: Wait for the provisioning script to complete. This might take about 5 minutes.

1. Verify that the script completed successfully and note the final message providing the URL containing the fully qualified name of the target Azure VM for the connection to the Windows Admin Center.
1. Close the Cloud Shell blade.

### Task 2: Review results of the script provisioning

1. In the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Resource groups** and, on the **Resource groups** blade, select the **ws2019-m031-rg** entry.
1. On the **ws2019-m031-rg** blade, on the **Overview** blade, review the list of resources, including the Azure VM **ws2019-wac-vm**.
1. In the list of resources, select the Azure VM **ws2019-wac-vm** entry, and on the **ws2019-wac-vm** blade, select **Networking**.
1. On the **ws2019-wac-vm | Networking** blade, on the **Inbound port rules** tab, note entries representing the inbound port rule allowing connectivity on TCP port 5986 and the inbound rule allowing connectivity on TCP port 443.

## Exercise 4: Verifying functionality of the Windows Admin Center gateway in Azure

### Task 1: Connect to the Windows Admin Center gateway running in Azure VM

1. On SEA-CL1, start Microsoft Edge and navigate to the URL containing the fully qualified name of the target Azure VM hosting the Windows Admin Center gateway you identified in the previous exercise.
1. In Microsoft Edge window, disregard the message **Your connection isn't private**, select **Advanced**, and then select the link starting with the text **Continue to**.
1. When prompted, in the **Sign in to access this site** dialog box, provide the following credentials and select **OK**:

*Table 2: Sign in credentials*

|Setting|Value|
|---|---|
|Username|Student|
|Password|Pa55w.rd1234|

4. On the **All connections** blade of the Windows Admin Center page, select **ws2019-wac-vm [Gateway]**.

5. On the **Specify your credentials** blade, select the **Use another account for this connection** is selected, specify the following credentials, and select **Continue**.

*Table 3: Sign in credentials*

|Setting|Value|
|---|---|
|Username|Student|
|Password|Pa55w.rd1234|

6. Examine the **Overview** blade of the Windows Admin Center page.

### Task 2: Enable PowerShell Remoting on an Azure VM

1. On SEA-CL1, in the Microsoft Edge window displaying the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Virtual machines**.
1. On the **Virtual machines** blade, select **ws2019-m03-vm0**.
1. On the **ws2019-m03-vm0** blade, in the **Operations** section, select **Run command** and then select **RunPowerShellScript**.
1. On the **Run Command Script** blade, in the **PowerShell Script** section, type the following to and select **Run**:

   ```pwsh
   winrm quickconfig -quiet
   ```

1. On the **Run Command Script** blade, in the **PowerShell Script** section, replace the text you typed in the previous step with the following to and select **Run**:

   ```pwsh
   Set-NetFirewallRule -Name WINRM-HTTP-In-TCP-PUBLIC -RemoteAddress Any
   ```

1. On the **Run Command Script** blade, in the **PowerShell Script** section, replace the text you typed in the previous step with the following to and select **Run**:

   ```pwsh
   Enable-PSRemoting -Force -SkipNetworkProfileCheck
   ```

### Task 3: Connect to an Azure VM by using the Windows Admin Center gateway running in Azure VM

1. On SEA-CL1, in the Microsoft Edge window displaying the interface of the Windows Admin Center gateway running on the **ws2019-wac-vm** Azure VM, select **Windows Admin Center**.
1. On the **All connections** page, select **+ Add**.
1. On the **Add or create resources** blade, in the **Servers** section, select **Add**.
1. In the **Server name** textbox, type **10.3.0.4** and select **Add**.
1. On the **Specify your credentials** blade, select the **Use another account for this connection** is selected, specify the following credentials, and select **Add with credentials**.

*Table 4: Sign in credentials*

|Setting|Value|
|---|---|
|Username|Student|
|Password|Pa55w.rd1234|

7. Once successfully connected, examine the **Overview** blade of the Windows Admin Center page on the second Azure VM with the IP address of **10.3.0.4**.

## Exercise 5: Deprovisioning the Azure environment

### Task 1: Start a PowerShell session in Cloud Shell

1. On SEA-CL1, switch to the Microsoft Edge window displaying the Azure portal.
1. In the Microsoft Edge window displaying the Azure portal, open the **Azure Cloud Shell** blade by selecting by selecting the Cloud Shell button in the Azure Portal.

### Task 2: Identify all Azure resources provisioned in the lab

1. From the Cloud Shell blade, run the following to list all resource groups created throughout this lab:

   ```pwsh
   Get-AzResourceGroup -Name 'ws2019-m03*'
   ```

1. From the Cloud Shell blade, run the following to delete all resource groups you created throughout this lab:

   ```pwsh
   Get-AzResourceGroup -Name 'ws2019-m03*' | Remove-AzResourceGroup -Force -AsJob
   ```

    >**Note**: The command executes asynchronously (as determined by the -AsJob parameter), so while you'll be able to run another PowerShell command immediately afterwards within the same PowerShell session, it will take a few minutes before the resource groups are actually removed.
