---
lab:
    title: 'Lab: Managing Azure VMs running Windows Server 2019'
    type: 'Answer Key'
    module: 'Module 7: Managing and maintaining Azure VMs'
---

# Lab answer key: Managing Azure VMs running Windows Server 2019

## Exercise 1: Provisioning Azure VMs running Windows Server 2019

### Task 1: Create a resource group

1. Sign in to **SEA-CL1** as **Contoso\\Administrator** with **Pa55w.rd** as the password.
1. On **SEA-CL1**, start Microsoft Edge, and then navigate to the [Azure portal](https://portal.azure.com). When prompted, sign in using a user account with the Owner role in the Azure subscription you will be using in this lab.
1. In the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Resource groups**.
1. On the **Resource groups** blade, select **+ Add**. 
1. On the **Create a resource group** blade, specify the following settings: 

   *Table 1: Resource group settings*
   
   |Setting|Value|
   |---|---|
   |Subscription|Use the name of the Azure subscription you will be using in this lab.|
   |Resource group|ws2019-07-rg1|
   |Region|Use the name of an Azure region in which you can provision Azure virtual machines.|

1. Select **Review + create**, wait for the validation process to complete, and then select **Create**.

### Task 2: Upload PowerShell scripts into Cloud Shell home directory

1. On **SEA-CL1**, in the Microsoft Edge browser displaying the Azure portal, open a new browser tab and, in the address bar, enter `https://shell.azure.com`. If prompted to authenticate, sign in using a user account with the Owner role in the Azure subscription you are using in this lab.

    > **Note**: If this is the first time you are starting **Cloud Shell** and you are presented with the **You have no storage mounted** message, select the subscription you are using in this lab, and select **Create storage**. 

1. Ensure that the **PowerShell** entry appears in the drop-down list directly under the **Azure Cloud Shell** header. If that's not the case, expand the drop down list and select **PowerShell**. 

1. Select the **Upload/Download files** icon on the top menu ribbon and, in the drop-down menu, select **Upload**. This will display the **Open** dialog box. 

1. In the **Open** dialog box, navigate to **C:\\Labfiles\\Mod07\\Scripts**, and then upload the following three files:
 
   - **Mod07Network.ps1**
   - **Mod07GW.ps1**
   - **Mod07TG.ps1**

### Task 3: Create two Azure VMs by using Azure Cloud Shell

1. On **SEA-CL1**, in the Microsoft Edge browser displaying the Cloud Shell, at the PowerShell prompt, enter the following command and select the **Enter** key to create virtual network resources:

   ```powershell
   ./Mod07Network.ps1
   ```

    > **Note**: Ignore warnings regarding changes to PowerShell syntax. 

1. In the Cloud Shell window, at the PowerShell prompt, enter the following command and select the **Enter** key to create the **Mod07Gateway** VM:

   ```powershell
   ./Mod07GW.ps1
   ```

1. When prompted, enter the following: 

   *Table 2: Mod07Gateway VM local Administrator credentials*

   |Setting|Value|
   |---|---|
   |User|Student|
   |Password|Pa55w.rd1234|

   > **Note**: It might take about 3-5 minutes to create the **Mod07Gateway** VM.

1. In the Cloud Shell window, at the PowerShell prompt, enter the following command and select the **Enter** key to create the **Mod07Target** VM:

   ```powershell
   ./Mod07TG.ps1
   ```

1. When prompted, enter the following: 

   *Table 3: Mod07Target VM local Administrator credentials*

   |Setting|Value|
   |---|---|
   |User|Student|
   |Password|Pa55w.rd1234|

   > **Note**: It might take approximately 3-5 minutes to create the **Mod07Target** VM.

   > **Note**: Leave the Azure Cloud Shell tab open - you'll use it later in this lab. After 20 minutes, the Cloud Shell will automatically disconnect, but you can select the **Reconnect** button to return to the PowerShell prompt.

## Exercise 2: Managing Azure VMs running Windows Server 2019 by using Windows Admin Center

### Task 1: Install Microsoft Edge and Windows Admin Center on the Mod07Gateway Azure VM

1. On **SEA-CL1**, in the Microsoft Edge window, switch to the tab displaying the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Virtual machines**.
1. On the **Virtual machines** blade, select the **Mod07Gateway** entry.
1. On the **Mod07Gateway** blade, in the top menu, select **Connect**, in the drop-down menu, select **RDP**, on the **Mod07Gateway \| Connect** blade, select **Download RDP File**, and, at the bottom of the browser page, select **Open**.
1. In the **Remote Desktop Connection** window, select **Connect**.
1. In the **Windows Security** window, in **User name** field, enter **Student**, in the **Password** field, enter **Pa55w.rd1234**, and then select **OK**.

   > **Note**: If the **Windows Security**, **Enter credentials** page displays the **CONTOSO\\Administrator** user name, select **More choices**, select **Use a different account**, and then enter the credentials provided in this step.

1. In the **Remote Desktop Connection** window, select **Yes**. 
1. Within the Remote Desktop session to the **Mod07Gateway** Azure VM, in the Networks bar, select **No** and wait for the Server Manager window to load.
1. In **Server Manager**, in the console tree, select **Local Server**.
1. In the details pane, select the **IE Enhanced Security Configuration** item and then select the **On** hyperlink next to it.
1. In the **Internet Explorer Enhanced Security Configuration** window, under **Administrators**, select the **Off** radio button, and then select **OK**.
1. In the taskbar, select **Internet Explorer**.
1. In the **Set up Internet Explorer 11** window, select **OK**.
1. Maximize **Internet Explorer**.
1. In the address bar, enter **Microsoft Edge**, select **Enter**, and then select the **Download now** button.
1. In the **Microsoft Edge** window, select **DOWNLOAD for Windows 10**.
1. In the install window, on the **Download the new Microsoft Edge** page, select **Accept and download**.
1. Select the **Run** button in the bar displayed at the bottom of the browser page.
1. After a few moments, after Microsoft Edge installs and then opens with the **Welcome** page, select **Get started**.
1. On the **Let's set up your new tab** pane, select **Confirm**. 
1. On the **See your favorites, passwords and many more on any device** pane, select **Continue without Signing-in**.
1. In the Microsoft Edge window, navigate to [Windows Admin Center](https://aka.ms/windows-admin-center-overview)	
1. Select the **Windows Admin Center** link in the **Download now** section to navigate to the Windows Admin Center section of Microsoft Evaluation Center.
1. In the **Start your evaluation** section, select **Continue**, enter requested information on the download form, select **Continue**, and then select **Download**.
1. When the download completes, select the **Open file** link and, in the **Open File - Security Warning** dialog box, select **Run**. This will start the **Windows Admin Center Setup** wizard.
1. On the first page of the setup wizard, select the **I accept these terms** check box, and then select **Next**.
1. In the subsequent three pages, select **Next** each time, accepting the defaults, and on the fourth page, select **Install**.
1. When the installation completes, in the **Windows Admin Center Setup** program, select **Finish**.

### Task 2: Add the Mod07Target VM to Windows Admin Center on Mod07Gateway VM

1. In the **Microsoft Edge** browser address bar, enter `https://Mod07Gateway`.
1. Within the Remote Desktop session to the **Mod07Gateway** Azure VM, in the Microsoft Edge window displaying **Windows Admin Center**, on the **All connections** page, select **+ Add**.
1. On the **Add or create resources** blade, in the **Servers** section, select **Add**.
1. In the **Server name** text box, enter **Mod07Target** and select **Add**.
1. On the **All connections** page, select **Mod07Target**.

### Task 3: Use Windows Admin Center to install the Internet Information Services (IIS) Web Server role on Mod07Target

1. Within the Remote Desktop session to **Mod07Gateway**, in the Microsoft Edge browser, open a new tab and, in the address bar, enter `http://mod07target`. Note that the target page can't be reached. Keep the tab open and switch back to the **Windows Admin Center** tab.
1. In **Windows Admin Center**, on the **Mod07Target** page, in the **Tools** listing, scroll to and select **Roles & features**.
1. In the **Roles and features** details pane, scroll to and select the **Web Server (IIS)** check box and then, in the top area of the details pane, select **+ Install**.
1. On the **Install Roles and Features** pane, in the **Continue installation?** section, select **Yes**.
1. In the **Tools** listing, select **Overview** and then, in the toolbar of the Overview pane, select **Refresh**. You can watch the CPU activity corresponding to the installation of the Web Server role.

   > **Note**: Completion of the installation will display a notification. You can review it by selecting the **Notifications** icon in the upper right corner of the page.

1. Once the installation completes, switch to the Microsoft Edge tab targeting the `http://mod07target` URL and refresh the browser page. Verify that the browser displays the **Internet Information Services Welcome** page.
1. Switch back to browser tab displaying **Windows AdminCenter**, in the details pane top toolbar, select **Shutdown**, and then in the **Shut down the computer** window, select **Yes**.
1. Leave the Remote Desktop connection to **Mod07Gateway** open.

## Exercise 3: Managing Windows Server 2019 running in Azure VMs by using PowerShell Remoting

### Task 1: Configure PowerShell Remoting of an Azure VM running Windows Server 2019

1. On **SEA-CL1**, in the Microsoft Edge window, switch to the tab displaying Azure Cloud Shell. If needed, select **Reconnect**.
1. In the Cloud Shell window, at the PowerShell prompt, enter the following command and select the **Enter** key to start the **Mod07Target** Azure VM:

   ```powershell
   Start-AzVM -ResourceGroupName ws2019-07-rg1 -Name Mod07Target
   ```

1. After the VM successfully starts, in the Cloud Shell window, at the PowerShell prompt, enter the following command and select the **Enter** key to configure PowerShell Remoting on the **Mod07Target** Azure VM:

   ```powershell
   Enable-AzVMPSRemoting -Name 'Mod07Target' -ResourceGroupName 'ws2019-07-rg1' -Protocol https -OsType Windows
   ```

   > **Note**: The **Enable-AzureVMPSRemoting** cmdlet configures WinRM on the target VM and configures its Network Security Group to allow access via Windows Remote Management.

### Task 2: Manage Windows Server 2019 running in an Azure VM by using PowerShell Remoting

1. In the Cloud Shell window, at the PowerShell prompt, enter the following command and select the **Enter** key to list Windows services which names begin with **Win** installed within the operating system of the **Mod07Target** Azure VM:

   ```powershell
   Invoke-AzVMCommand -Name 'Mod07Target' -ResourceGroupName 'ws2019-07-rg1' -ScriptBlock {get-service win*} -Credential $Cred
   ```

1. At the **User:** prompt, enter **Student**, and then select the **Enter** key.
1. At the **Password for user Student:** prompt, enter **Pa55w.rd1234**, and then select the **Enter** key. 
1. Review the results displayed in the Cloud Shell pane and verify that they include a list of services which names begin with **Win**. 
1. In the Cloud Shell window, at the PowerShell prompt, enter the following command and select the **Enter** key to start an interactive PowerShell Remoting session within the operating system of the **Mod07Target** Azure VM:

   ```powershell
   Enter-AzVM -Name Mod07Target -ResourceGroupName ws2019-07-rg1 -Credential $Cred
   ```

1. At the **User:** prompt, enter **Student**, and then select the **Enter** key.
1. At the **Password for user Student:** prompt, enter **Pa55w.rd1234**, and then select the **Enter** key. 

   > **Note**: You will be presented with an interactive session prompt. This allows you to run Windows PowerShell cmdlets directly against the target VM until you exit the session.

1. At the PowerShell Remoting session, enter the following command and select the **Enter** key to list locally installed Windows services which names begin with **Win**:

   ```powershell
   Get-Service Win*
   ```

   > **Note**: The results should include the same list of services that you reviewed earlier in this task.

1. At the PowerShell Remoting session, enter the following command and select the **Enter** key to exit the interactive PowerShell Remoting session.

   ```powershell
   Exit
   ```

> **Note**: Keep the **Cloud Shell** tab open. You will use it again later in this lab.


## Exercise 4: Managing Windows Server 2019 running in Azure VMs by using Run command

### Task 1: Use EnableRemotePS command

1. On **SEA-CL1**, on the Microsoft Edge tab displaying the Azure Portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Virtual machines**.
1. On the **Virtual machines** blade, select the **Mod07Target** entry.
1. On the **Mod07Target** blade, in the console tree, scroll down to the **Operations** section and select the **Run command** item.
1. On the **Mod07Target \| Run command** blade, select the **EnableRemotePS** item. 
1. On the **Run Command Script** blade, select **Script**, review the content of the script that enables PowerShell Remoting on the target operating system, and select **Run**.
1. Wait until script execution completes.

### Task 2: Use RunPowerShellScript command

1. On **SEA-CL1**, on the Microsoft Edge tab displaying the Azure Portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Virtual machines**.
1. On the **Virtual machines** blade, select the **Mod07Target** entry.
1. On the **Mod07Target** blade, in the console tree, scroll down to the **Operations** section and select the **Run command** item.
1. On the **Mod07Target \| Run command** blade, select the **RunPowerShellScript** item. 
1. On the **Run Command Script** blade, in the **PowerShell Script** pane, enter `Get-Service Win*` and select **Run**.
1. Wait until script execution completes, examine the results, and verify that they include the same list of services that you reviewed in the previous task.
1. Close the **Run Command Script** blade.


## Exercise 5: Managing Windows Server 2019 in Azure VMs by using the serial console

### Task 1: Create a storage account

> **Note**: Storage account names must be globally unique and can contain between 3 and 24 characters, including lowercase letters and numbers, starting with a letter.

1. On **SEA-CL1**, in the Microsoft Edge browser, in the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Storage accounts**.
1. On the **Storage accounts** blade, select **+ Add**.
1. On the **Basics** tab of the **Create storage account** blade, specify the following settings (leave others with their default values):

   *Table 4: Storage account settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|ws2019-07-rg1|
   |Storage account name|any globally unique name between 3 and 24 in length consisting of letters and digits|
   |Location|the name of the Azure region where you created the resource group in the previous task|
   |Performance|Standard|
   |Account kind|Storage (general purpose v1)|
   |Replication|Locally redundant storage (LRS)|

1. On the **Basics** tab of the **Create storage account** blade, select **Review + Create**, wait for the validation process to complete, and select **Create**.

   > **Note**: Wait for the Storage account to be created. This should take about 2 minutes.

### Task 2: Configure boot diagnostics for an Azure VM

1. On **SEA-CL1**, in the Microsoft Edge window displaying the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Virtual machines**.
1. On the **Virtual machines** blade, select **Mod07Target**.
1. In the console tree, scroll down to the **Support + troubleshooting** section and select **Boot diagnostics**.
1. On the **Mod07Target \| Boot diagnostics** blade, in the top menu bar, select **Settings**.
1. On the **Boot diagnostics** blade, select the **Enable with custom storage account** option, in the **Diagnostics storage account** section, select the storage account you created earlier in this exercise, and select **Save**.

   > **Note**: Managed storage account does not support the serial console functionality. 


### Task 3: Use the serial console

1. On **SEA-CL1**, on the Microsoft Edge tab displaying the Azure Portal, on the **Mod07Target** blade, in the console tree, scroll down to the **Support + troubleshooting** section and select **Serial console**. 

   > **Note**: The **Mod07Target \| Serial console** blade should display the **SAC> ** prompt

1. On **Mod07Target \| Serial console** blade, at the **SAC> ** prompt, enter **cmd** and select the **Enter** key to create a channel that contains a CMD instance.
1. On **Mod07Target \| Serial console** blade, at the **SAC> ** prompt, enter **ch -si 1** and select the **Enter** key to switch to the channel that's running the CMD instance.
1. On **Mod07Target \| Serial console** blade, select the **Enter** key, and then enter the following sign-in credentials of the local Administrator account:

   *Table 5: Mod07Target VM local Administrator credentials*

   |Setting|Value|
   |---|---|
   |User|Student|
   |Domain|Mod07Target|
   |Password|Pa55w.rd1234|


1. On **Mod07Target \| Serial console** blade, at the **C:\\windows\\system32** prompt, enter the following commands, selecting the Enter key after each command in order to configure operating system boot options:

   ```cmd
   bcdedit /set {bootmgr} displaybootmenu yes
   bcdedit /set {bootmgr} timeout 20
   bcdedit /set {bootmgr} bootems yes
   shutdown -r -t 0
   ```

1. On **Mod07Target \| Serial console** blade, when the EMS window displays, select the **F8** key to display the **Advanced Boot Option** screen.
1. On **Mod07Target \| Serial console** blade, within the serial console session, use the arrows on the keyboard to navigate the **Advanced Boot Option** screen and select the **Start Windows normally** option.  
1. On **Mod07Target \| Serial console** blade, exit the serial console by selecting the **Overview** node in the console tree.

   > **Note**: The **Advanced Boot options** screen displays the message, **Choose Advanced Options for: Windows server 2016**. Mod07Target is running Windows Server 2019. This is expected, since the serial console code has not been updated on Windows Server 2019.

## Exercise 6: Managing Windows Server 2019 in Azure VMs by using Azure Policy Guest Configuration

### Task 1: Enable the Guest Configuration resource provider.

1. On **SEA-CL1**, in the Microsoft Edge window, switch to the tab displaying Azure Cloud Shell. If needed, select **Reconnect**.
1. In the Cloud Shell window, at the PowerShell prompt, enter the following command and select the **Enter** key to register the Guest Configuration resource provider:

   ```powershell
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
   ```
1. Wait until the resource provider is registered.

   > **Note**: To verify the registration status, you can re-run the **Register-AzResourceProvider** cmdlet.

### Task 2: Assign an Azure Policy Guest Configuration by using the Azure portal

1. On SEA-CL1, in the Microsoft Edge window displaying the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, search for and select **Policy**.
1. On the **Policy** blade, in the **Authoring** section, select **Definitions**.
1. On the **Policy \| Definitions** blade, in the filter bar above the details pane, in the **Category** column, select the  **All categories** drop-down list.
1. In the list, clear the check box **Select all**, and then select **Guest Configuration** only.
1. In the filter bar above the details pane, in the **Definition type** column, select the **All Definitions** drop-down list, and then select **Initiative**.
1. In the details pane, select **Audit Windows VMs on which the specified services are not installed and 'Running'** item.

   > **Note**: Alternatively, you can enter **Audit Windows VMs on which the specified services are not installed and 'Running'** in the **Search** text box.

1. On the **Audit Windows VMs on which the specified services are not installed and 'Running'** blade, select **Assign**.
1. On the **Basics** tab of the **Audit Windows VMs on which the specified services are not installed and 'Running'** blade, select the ellipses icon next to the **Scope** text box.
1. On the **Scope** blade, in the **Subscription** drop-down list, select your subscription, in the **Resource group** drop-down list, select **ws2019-07-rg1**, and then select **Select**.
1. Back on the **Basics** tab of the **Audit Windows VMs on which the specified services are not installed and 'Running'** blade, in the **Description** text box, enter **Auditing Windows services settings** and then select **Next**.
1. In the **Parameters** tab of the **Audit Windows VMs on which the specified services are not installed and 'Running'** blade, in the **Service names (supports wildcards)** text box, enter **W3SVC** and select **Next**.

   > **Note**: **W3SVC** is the name of the World Wide Web Publishing Service, which you installed on **Mod07Target** earlier in this lab.

1. On the **Remediations** tab of the **Audit Windows VMs on which the specified services are not installed and 'Running'** blade, select the **Create a remediation task** checkbox, leave the default entry in the **Policy to remediate** drop-down list, in the **Managed identity location** drop-down list, select the Azure region into which you deploye all resources in this lab, and select **Next**.
1. On the **Review + create** tab of the **Audit Windows VMs on which the specified services are not installed and 'Running'** blade, select **Create**.

### Task 3: Review results of the Guest Configuration policy.

1. On **SEA-CL1**, in the Microsoft Edge window displaying the Azure portal, navigate back to the **Policy \| Definitions** blade and, in the console tree, select **Overview**. 

1. On the **Policy** blade, note the **Audit Windows VMs on which the specified services are not installed and 'Running'** entry and verify that its **Compliance state** is listed as **Not started** 

1. On **SEA-CL1**, in the Microsoft Edge window, switch to the tab displaying Azure Cloud Shell. If needed, select **Reconnect**.

1. In the Cloud Shell window, at the PowerShell prompt, enter the following command and select the **Enter** key to trigger an on-demand Azure Policy compliance scan targeting resources in the resource group **ws2019-07-rg1**:

   ```powershell
   Start-AzPolicyComplianceScan -ResourceGroupName 'ws2019-07-rg1'
   ```

   > **Note**: Wait for the compliance scan to complete. For information regarding time it takes to complete different types of Azure Policy processing, refer to [Evaluation triggers](https://aka.ms/evaluation-triggers) and [Validation frequency](https://aka.ms/guest-configuration-validation-frequency). 

   > **Note**: Since you installed Web Server (IIS) server role on **Mod07Target**, that VM should be listed as compliant. However, since the resource group **ws2019-07-rg1** also contains the **Mod07Gateway** VM which does not include the Web Server (IIS) server role, the state of that resource and the assignment should be listed as **Non-compliant**.

1. To verify compliance status of individual resources, on **SEA-CL1**, in the Microsoft Edge window displaying the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Virtual machines**.

1. On the **Virtual machines** blade, select the **Mod07Target** entry.

1. On the **Mod07Target** blade, in the console tree, scroll to the **Operations** section, and then select **Policies**.

1. Verify that the **Audit Windows VMs on which the specified services are not installed and 'Running'** policy is listed as **Compliant**.

1. Navigate back to the **Virtual machines** blade and select the **Mod07Gateway** entry.

1. On the **Mod07Gateway** blade, in the console tree, scroll to the **Operations** section, and then select **Policies**.

1. Verify that the **Audit Windows VMs on which the specified services are not installed and 'Running'** policy is listed as **Non-compliant**.


## Exercise 7: Deprovisioning the Azure lab environment

### Task 1: Remove the policy assignment

1. On SEA-CL1, in the Microsoft Edge window displaying the Azure portal, in the **Search resources, services, and docs** text box at the top of the Azure portal page, search for and select **Policy**.
1. On the **Policy** blade, in the **Authoring** section, select **Assignments**.
1. On the **Policy \| Assignments** blade, right-click or access the context menu for the **Audit Windows VMs on which the specified services are not installed and 'Running'** assignment and, in the context menu, select **Delete assignment**.
1. When prompted for confirmation, select **Yes**.

### Task 2: Delete the ws2019-07-rg1 resource group

1. In the Azure portal, in the **Search resources, services, and docs** text box in the toolbar, search for and select **Resource groups**.
1. On the **Resource groups** blade, select **ws2019-07-rg1**.
1. On the **ws2019-07-rg1** blade, in the toolbar, select **Delete resource group**.
1. When prompted for confirmation, on the **Are you sure you want to delete "ws2019-07-rg1"?** blade, in the **TYPE THE RESOURCE GROUP NAME:** text box, enter **ws2019-07-rg1**, and then select **Delete**.
