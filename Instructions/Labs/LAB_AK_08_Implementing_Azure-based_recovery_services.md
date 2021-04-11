---
lab:
    title: 'Lab: Implementing Azure-based recovery services'
    type: 'Answer Key'
    module: 'Module 8: Planning and implementing migration and recovery services in hybrid scenarios'
---

# Lab answer key: Implementing Azure-based recovery services

## Exercise 1: Implementing the lab environment

#### Task 1: Deploy an Azure VM running Windows Server 2019 with the Hyper-V role installed

1. Sign in to **SEA-CL1** as **Contoso\\Administrator** with **Pa55w.rd** as the password.
1. On **SEA-CL1**, start Microsoft Edge, and then navigate to the [Azure portal](https://portal.azure.com). When prompted, sign in using a user account with the Owner role in the Azure subscription you will be using in this lab.
1. In the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Resource groups**.
1. On the **Resource groups** blade, select **+ Add**. 
1. On the **Create a resource group** blade, specify the following settings: 

   *Table 1: Resource group settings*

   |Setting|Value|
   |---|---|
   |Subscription|Use the name of the Azure subscription you will be using in this lab.|
   |Resource group|ws2019-08-rg1|
   |Region|Use the name of an Azure region in which you can provision Azure virtual machines.|

1. Select **Review + create**, wait for the validation process to complete, and then select **Create**.
1. On **SEA-CL1**, open another Microsoft Edge tab, navigate to [301-nested-vms-in-virtual-network Azure QuickStart template](https://aka.ms/301-nested-vms-in-virtual-network), and then select **Deploy to Azure**. This will automatically redirect the browser to the **Hyper-V Host Virtual Machine with nested VMs** blade in the Azure portal.
1. On the **Hyper-V Host Virtual Machine with nested VMs** blade in the Azure portal, specify the following settings (leave the others with their default values):

   *Table 2: QuickStart template deployment settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|ws2019-08-rg1|
   |Host Public IP Address Name|ws2019-08-hvm0-pip|
   |Virtual Network Name|ws2019-08-hv-vnet|
   |Host Network Interface1Name|ws2019-08-hvm0-nic1|
   |Host Network Interface2Name|ws2019-08-hvm0-nic2|
   |Host Virtual Machine Name|ws2019-08-hvm0|
   |Host Admin Username|Student|
   |Host Admin Password|Pa55w.rd1234|

1. On the **Hyper-V Host Virtual Machine with nested VMs** blade, select **Review + create** and then select **Create**.

   > **Note:** Wait for the deployment to complete. The deployment might take about 10 minutes.

#### Task 2: Connect to the Azure VM running the Windows Server 2019 with the Hyper-V role installed

1. On **SEA-CL1**, on the Microsoft Edge tab displaying the Azure Portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Virtual machines**.
1. In the Azure portal, on the **Virtual machines** blade, select **ws2019-08-hvm0**.
1. On the **ws2019-08-hvm0** blade, select **Networking**. 
1. On the **ws2019-08-hvm0 | Networking** blade, select **ws2019-08-hvm0-nic1** and then select **Add inbound port rule**.

    > **Note:** Make sure that you modify the settings of **ws2019-08-hvm0-nic1**, which has the public IP address assigned to it.

1. On the **Add inbound security rule** blade, specify the following settings (leave the others with their default values), and then select **Add**:

   *Table 3: Inbound security rule settings*

   |Setting|Value|
   |---|---|
   |Destination port ranges|3389|
   |Protocol|Any|
   |Name|AllowRDPInBound|

1. On the **ws2019-08-hvm0 \| Networking** blade, select **Overview**. 
1. On the **ws2019-08-hvm0** blade, in the top menu, select **Connect**, in the drop-down menu, select **RDP**, on the **ws2019-08-hvm0 \| Connect** blade, select **Download RDP File**, and, at the bottom of the browser page, select **Open**.
1. In the **Remote Desktop Connection** window, select **Connect**.
1. In the **Windows Security** window, in **User name** field, enter **Student**, in the **Password** field, enter **Pa55w.rd1234**, and then select **OK**.

   > **Note:** If the **Windows Security**, **Enter credentials** page displays the **CONTOSO\\Administrator** user name, select **More choices**, select **Use a different account**, and then enter the credentials provided in this step.

1. In the **Remote Desktop Connection** window, select **Yes**. 
1. Within the Remote Desktop session to the **ws2019-08-hvm0** Azure VM, in the Networks bar, select **No** and wait for the Server Manager window to load.

#### Task 3: Download a Windows Server 2019 VHD file

1. Within the Remote Desktop session to the **ws2019-08-hvm0** Azure VM, in **Server Manager**, in the console tree, select **Local Server**.

1. In the details pane, select the **IE Enhanced Security Configuration** item and then select the **On** hyperlink next to it.

1. In the **Internet Explorer Enhanced Security Configuration** window, under **Administrators**, select the **Off** option, and then select **OK**.

1. In the taskbar, select **Internet Explorer**.

1. In the **Set up Internet Explorer 11** window, select **OK**.

1. In Internet Explorer, browse to the Windows Server Evaluations Microsoft website and scroll down to the **Windows Server 2019** section. 

1. In the **Start your evaluation** subsection of the **Windows Server 2019** section, select the **VHD** option, select **Continue**, enter requested information on the download form, select **Continue**, and then select **Download**.

1. When prompted, in the browser window, at the bottom of the page, select **Save** and wait for the download to complete.

1. When the download completes, in the browser window, at the bottom of the page, select **Open folder**.

1. In the **File Explorer** window, right-click or access the context menu for the newly downloaded VHD file, and then select **Cut**. In the navigation pane, right-click or access the context menu for **Hyper-V (F:)**, select **New**, and then select **Folder**. In the details pane, select **New folder** and select its name again, enter **VHDs** to rename it, and select the Enter key twice to open it. 

1. Right-click or access the context menu for the empty area in the details pane and then select **Paste** to paste the VHD file you copied in the previous step.

#### Task 4: Deploy a Windows Server 2019 Hyper-V VM within an Azure VM

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Server Manager window, select **Tools** and, in the drop-down menu, select **Hyper-V Manager**. 
1. In the **Hyper-V Manager** console, select the **ws2019-08-hvm0** node, select **New** and, in the cascading menu, select **Virtual Machine**. This will start the **New Virtual Machine Wizard**. 
1. On the **Before You Begin** page of the **New Virtual Machine Wizard**, select **Next >**.
1. On the **Specify Name and Location** page of the **New Virtual Machine Wizard**, specify the following settings, and then select **Next >**:

   *Table 4: New virtual machine name and location settings*

   |Setting|Value|
   |---|---|
   |Name|ws2019-08-vm1|
   |Store the virtual machine in a different location|selected|
   |Location|F:\VMs|

1. On the **Specify Generation** page of the **New Virtual Machine Wizard**, ensure that the **Generation 1** option is selected, and then select **Next >**:
1. On the **Assign Memory** page of the **New Virtual Machine Wizard**, set **Startup memory** to **2048**, select the **Use Dynamic Memory for this virtual machine** checkbox, and select **Next >**.
1. On the **Configure Networking** page of the **New Virtual Machine Wizard**, in the **Connection** drop-down list, select **NestedSwitch**, and then select **Next >**.
1. On the **Connect Virtual Hard Disk** page of the **New Virtual Machine Wizard**, select the option **Use an existing virtual hard disk**, select **Browse**. In the **Open** window, browse to the **F:\\VHDs** folder, select the VHD file you downloaded in the previous task, and select **Open**. 
1. Back on the **Connect Virtual Hard Disk** page, select **Next >**.
1. On the **Summary** page of the **New Virtual Machine Wizard**, select **Finish**.
1. In the **Hyper-V Manager** console, select the newly created virtual machine and, in the **Actions** pane, select **Start**. 
1. In the **Hyper-V Manager** console, verify that the virtual machine is running and, in the **Actions** pane, select **Connect**. 
1. In the Virtual Machine Connection window to **ws2019-08-vm1**, on the **Hi there** page, select **Next**. 
1. In the Virtual Machine Connection window to **ws2019-08-vm1**, on the **License terms** page, select **Accept**. 
1. In the Virtual Machine Connection window to **ws2019-08-vm1**, on the **Customize settings** page, set the password of the built-in Administrator account to **Pa55w.rd1234** and select **Finish**. 
1. In the Virtual Machine Connection window to **ws2019-08-vm1**, select the **Action** menu and, in the drop-down menu, select **Ctrl+Alt+Delete**. 
1. In the **Password** text box, enter **Pa55w.rd1234** and select the **Enter** key.
1. In the Virtual Machine Connection window to **ws2019-08-vm1**, right-click or access the context menu for the **Start** menu icon, and then select **Windows PowerShell (Admin)**. 
1. From the **Administrator: Windows PowerShell** window, run the following to set the computer name:

   ```powershell
   Rename-Computer -NewName 'ws2019-08-vm1' -Restart
   ```

   > **Note:** The command will rename the operating system and restart it.
   
## Exercise 2: Creating and configuring an Azure Site Recovery vault

#### Task 1: Create an Azure Site Recovery vault

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Internet Explorer window, navigate to the [Azure portal](https://portal.azure.com), and sign in by providing credentials of a user account with the Owner role in the subscription you are using in this lab.
1. In the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Recovery Services vaults** and, on the **Recovery Services vaults** blade, select **+ Add**.
1. On the **Basics** tab of the **Create Recovery Services vault** blade, specify the following settings (leave others with their default values) and select **Review + create**:

   *Table 5: Recovery Services vault settings*

   |Setting|Value|
   |---|---|
   |Subscription|The name of the Azure subscription you are using in this lab|
   |Resource group|Select Create new, in the Name text box, enter ws2019-08-rg2 and select OK|
   |Vault name|ws2019-08a-rsvault|
   |Location|The name of an Azure region different from the one into which you deployed the Azure VM in the first exercise of this lab|

1. On the **Review + create** tab of the **Create Recovery Services vault** blade, select **Create**:

    > **Note:** By default, the default configuration for Storage Replication type is set to Geo-redundant (GRS) and Soft Delete is enabled. You will change these settings in the lab to simplify deprovisioning, but you should use them in your production environments.

#### Task 2: Configure the Azure Site Recovery vault

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Recovery Services vaults** and, on the **Recovery Services vaults** blade, select **ws2019-08a-rsvault**.
1. On the **ws2019-08a-rsvault** blade, in the vertical menu, in the **Settings** section, select **Properties**. 
1. On the **ws2019-08a-rsvault | Properties** blade, select the **Update** link under the **Backup Configuration** label.
1. On the **Backup Configuration** blade, set **Storage replication type** to **Locally-redundant**, select **Save** and close the **Backup Configuration** blade.

   > **Note:** Storage replication type cannot be changed once you implement protection.

1. On the **ws2019-08a-rsvault | Properties** blade, select the **Update** link under the **Security Settings** label.
1. On the **Security Settings** blade, set **Soft Delete** to **Disable**, select **Save** and close the **Security Settings** blade.

## Exercise 3: Implementing Hyper-V VM protection by using Azure Site Recovery vault

#### Task 1: Implement an Azure recovery site

1. In the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Virtual networks** and, on the **Virtual networks** blade, select **+ Add**.
1. On the **Basics** tab of the **Create virtual network** blade, specify the following settings (leave others with their default values) and select **Next: IP Addresses**:

   *Table 6: Virtual network **ws2019-08-dr-vnet** settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|select Create new, in the Name text box, enter ws2019-08-rg3 and select OK|
   |Name|ws2019-08-dr-vnet|
   |Region|the name of the Azure region into which you deployed the Recovery Services vault earlier in this lab|

1. On the **IP addresses** tab of the **Create virtual network** blade, select the recycle bin icon, in the **IPv4 address space** text box, enter **10.8.0.0/22** and select **+ Add subnet**.
1. On the **Add subnet** blade, specify the following settings (leave others with their default values) and select **Add**:

   *Table 7: Virtual network **ws2019-08-dr-vnet** subnet **subnet0** settings*

   |Setting|Value|
   |---|---|
   |Subnet name|subnet0|
   |Subnet address range|10.8.0.0/24|

1. Back on the **IP addresses** tab of the **Create virtual network** blade, select **Review + create**.
1. On the **Review + create** tab of the **Create virtual network** blade, select **Create**.
1. In the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Virtual networks** and, on the **Virtual networks** blade, select **+ Add**.
1. On the **Basics** tab of the **Create virtual network** blade, specify the following settings (leave others with their default values) and select **Next: IP Addresses**:

   *Table 8: Virtual network **ws2019-08-test-vnet** settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|ws2019-08-rg3|
   |Name|ws2019-08-test-vnet|
   |Region|the name of the Azure region into which you deployed the Recovery Services vault earlier in this lab|

1. On the **IP addresses** tab of the **Create virtual network** blade, select the recycle bin icon, in the **IPv4 address space** text box, enter **10.0.0.0/22** and select **+ Add subnet**.
1. On the **Add subnet** blade, specify the following settings (leave others with their default values) and select **Add**:

   *Table 9: Virtual network **ws2019-08-test-vnet** subnet **subnet3** settings*

   |Setting|Value|
   |---|---|
   |Subnet name|subnet3|
   |Subnet address range|10.0.2.0/24|

   > **Note:** This matches the IP address range of the production network and the subnet containing the Hyper-V that needs to be protected. 

1. Back on the **IP addresses** tab of the **Create virtual network** blade, select **Review + create**.
1. On the **Review + create** tab of the **Create virtual network** blade, select **Create**.
1. In the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Storage accounts** and, on the **Storage accounts** blade, select **+ Add**.
1. On the **Basics** tab of the **Create storage account** blade, specify the following settings (leave others with their default values):

   *Table 10: Storage account settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|ws2019-08-rg3|
   |Storage account name|any globally unique name between 3 and 24 in length consisting of letters and digits, starting with a letter|
   |Location|the name of the Azure region into which you deployed the Recovery Services vault earlier in this lab|
   |Performance|Standard|
   |Account kind|Storage (general purpose v1)|
   |Replication|Locally redundant storage (LRS)|

1. On the **Basics** tab of the **Create storage account** blade, select **Review + create**.
1. On the **Review + create** tab of the **Create storage account** blade, select **Create**.

#### Task 2: Prepare protection of a Hyper-V virtual machine

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Recovery Services vaults** and, on the **Recovery Services vaults** blade, select the **ws2019-08a-rsvault** entry.
1. On the **ws2019-08a-rsvault** blade, in the vertical menu, in the **Getting started** section, select **Site Recovery**
1. On the **ws2019-08a-rsvault \| Site Recovery** blade, in the **Hyper-V machines to Azure** section, select **1. Prepare infrastructure**. 
1. On the **Deployment planning** tab of the **Prepare infrastructure** blade, in the **Deployment planning completed?** drop-down list, select **Yes, I have done it** and select **Next**.
1. On the **Source settings** tab of the **Prepare infrastructure** blade, next to the **Are you Using System Center VMM to manage Hyper-V hosts** label, select the **No** option.
1. On the **Source settings** tab of the **Prepare infrastructure** blade, select the **Add Hyper-V site** link, on the **Create Hyper-V Site** blade, in the **Name** text box, enter **ws2019-08 Hyper-V site** and select **OK**:
1. On the **Source settings** tab of the **Prepare infrastructure** blade, select the **Add Hyper-V server** link. 
1. On the **Add Server** blade, select the **Download** link in step 3 of the procedure for adding on-premises Hyper-V hosts in order to download the Microsoft Azure Site Recovery Provider.
1. When prompted, in the browser window, select **Run** to launch **AzureSiteRecoveryProvider.exe**. This will start the **Azure Site Recovery Provider Setup (Hyper-V server)** wizard.
1. On the **Microsoft Update** page, select **Off** and select **Next**.
1. On the **Provider installation** page, select **Install**.
1. Switch to the Azure portal and, on the **Add Server** blade, select the **Download** button in step 4 of the procedure for registering on-premises Hyper-V hosts in order to download the vault registration key. When prompted, select **Save** to save the vault credentials file in the **Downloads** folder.
1. Switch to the **Provider installation** wizard window and select **Register**. This will start the **Microsoft Azure Site Recovery Registration Wizard**.
1. On the **Vault Settings** page of the **Microsoft Azure Site Recovery Registration Wizard**, select **Browse**, in the **Open** window, navigate to the **Downloads** folder, select the vault credentials file, and select **Open**.
1. Back on the **Vault Settings** page of the **Microsoft Azure Site Recovery Registration Wizard**, select **Next**.
1. On the **Proxy Settings** page of the **Microsoft Azure Site Recovery Registration Wizard**, accept the default settings and select **Next**.
1. On the **Registration** page of the **Microsoft Azure Site Recovery Registration Wizard**, select **Finish**.
1. Switch back to the Internet Explorer window displaying the Azure portal and refresh the page. When prompted, select **Leave this page**. 
1. Back on the **ws2019-08a-rsvault | Site Recovery** blade, in the **Hyper-V machines to Azure** section, select **1. Prepare infrastructure**. 
1. On the **Deployment planning** tab of the **Prepare infrastructure** blade, in the **Deployment planning completed?** drop-down list, select **Yes, I have done it** and select **Next**.
1. On the **Source settings** tab of the **Prepare infrastructure** blade, next to the **Are you Using System Center VMM to manage Hyper-V hosts** label, select the **No** option.
1. Verify that the **Hyper-V site** and **Hyper-V servers** settings are set correctly and select **Next**. 
1. On the **Target settings** tab of the **Prepare infrastructure** blade, accept the default settings and select **Next**.
1. On the **Replication policy** tab of the **Prepare infrastructure** blade, select **Create new policy and associate**. 
1. On the **Create and associate policy** blade, specify the following settings (leave others with their default values) and select **OK**:

   *Table 11: Policy settings*

   |Setting|Value|
   |---|---|
   |Name|ws2019-08 replication policy|
   |Copy frequency|30 seconds|

1. Back on the **Replication policy** tab of the **Prepare infrastructure** blade, wait until the site has been associated with the policy and select **Next**.
1. On the **Review** tab of the **Prepare infrastructure** blade, select **Prepare**.

#### Task 3: Enable replication of a Hyper-V virtual machine

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, on the **ws2019-08a-rsvault \| Site Recovery** blade, in the **Hyper-V machines to Azure** section, select **2. Enable replication**. 
1. On the **Source environment** tab of the **Enable replication** blade, in the **Source location** drop-down list, select **ws2019-08 Hyper-V site** and select **Next**.
1. On the **Target environment** tab of the **Enable replication** blade, specify the following settings (leave others with their default values) and select **Next**:

   *Table 12: Target environment settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Post-failover resource group|ws2019-08-rg3|
   |Post-failover deployment model|Resource Manager|
   |Storage account|the name of the storage account you created in the first task of this exercise|
   |Azure network|Configure now for selected machines|
   |Virtual network|ws2019-08-dr-vnet|
   |Subnet|subnet0 (10.8.0.0/24)|

1. On the **Virtual machine selection** tab of the **Enable replication** blade, select the **ws2019-08-vm1** checkbox and select **Next**.
1. On the **Replication settings** tab of the **Enable replication** blade, in the **Defaults** row and **OS type** column, select **Windows** from the drop-down list and select **Next**.
1. On the **Replication policy** tab of the **Enable replication** blade, accept the default settings and select **Next**.
1. On the **Review** tab of the **Enable replication** blade, select **Enable replication**.

#### Task 4: Review Azure VM replication settings

1. In the Azure portal, back on the **ws2019-08a-rsvault \| Site Recovery** blade, in the vertical menu, select **Replicated items**. 
1. On the **ws2019-08a-rsvault \| Replicated items** blade, ensure that there is an entry representing the **ws2019-08-vm1** virtual machine and verify that its **Replication Health** is listed as **Healthy** and that its **Status** is listed as either **Enabling protection** or displaying a current percentage of synchronization progress.

   > **Note:** You might need to wait a few minutes until the **ws2019-08-vm1** entry appears on the **ws2019-08a-rsvault \| Replicated items** blade.

1. On the **ws2019-08a-rsvault \| Replicated items** blade, select the **ws2019-08-vm1** entry.
1. On the **ws2019-08-vm1** replicated items blade, review the **Health and status**, **Failover readiness**, **Latest recovery points**, and **Infrastructure view** sections. Note the **Planned Failover**, **Failover** and **Test Failover** toolbar icons.

   > **Note:** Wait until the status changes to **Protected**. This might take additional 15 minutes. You will need to refresh the browser page for the status to be updated.

1. On the **ws2019-08-vm1** replicated items blade, select **Latest recovery points** and review **Latest crash-consistent** and **Latest app-consistent** recovery points. 

#### Task 5: Perform a failover of the Hyper-V virtual machine

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the browser window displaying the Azure portal, on the **ws2019-08-vm1** replicated items blade, select **Test failover**. 
1. On the **Test failover** blade, specify the following settings (leave others with their default values) and select **OK**:

   *Table 13: Test failover settings*

   |Setting|Value|
   |---|---|
   |Choose a recovery point|the default option|
   |Azure virtual network|ws2019-08-test-vnet|

1. In the Azure portal, navigate back to the **ws2019-08a-rsvault** blade and, in the vertical menu, in the **Monitoring** section, select **Site Recovery jobs**. Wait until the status of the **Test failover** job is listed as **Successful**.
1. In the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Virtual machines** and, on the **Virtual machines** blade, note the entry representing the newly provisioned virtual machine **ws2019-08-vm1-test**.
1. In the Azure portal, navigate back to the on the **ws2019-08-vm1** replicated item blade and select **Cleanup test failover**.
1. On the **Test failover cleanup** blade, select the checkbox **Testing is complete. Delete test failover virtual machine(s)** and select **OK**.
1. Once the test failover cleanup job completes, refresh the browser page displaying the **ws2019-08-vm1** replicated items blade and note that you have the option to perform planned and unplanned failover.
1. On the **ws2019-08-vm1** replicated items blade, select **Planned failover**. 
1. On the **Planned failover** blade, note that the failover direction settings are already set and not modifiable. 
1. Close the **Planned failover** blade without initiating a failover and, on the **ws2019-08-vm1** replicated items blade, select **Failover**. 
1. On the **Failover** blade, note that you have the option to choose a recovery point. 
1. Close the **Failover** blade without initiating a failover.


## Exercise 4: Implementing Azure Backup

#### Task 1: Create an Azure Site Recovery vault

> **Note:** While, in general, the same vault can be used to implement Azure Site Recovery and Azure Backup functionality, the one hosting Azure Backup should be located close to the location of the backed up items. For this reason, you will create another Azure Site Recovery vault in the same Azure region as the Azure VM you deployed in the second exercise of this lab. 

1. Within the Remote Desktop session to **ws2019-08-hvm0**, switch to the Virtual Machine Connection window to **ws2019-08-vm1**.
1. In the Virtual Machine Connection window to **ws2019-08-vm1**, in **Server Manager**, in the console tree, select **Local Server**.
1. In the details pane, select the **IE Enhanced Security Configuration** item and then select the **On** hyperlink next to it.
1. In the **Internet Explorer Enhanced Security Configuration** window, under **Administrators**, select the **Off** option, and then select **OK**.
1. In the taskbar, select **Internet Explorer**.
1. In the **Set up Internet Explorer 11** window, select **OK**.
1. In Internet Explorer, navigate to the [Azure portal](https://portal.azure.com). When prompted, sign in using a user account with the Owner role in the Azure subscription you will be using in this lab.
1. In the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Recovery Services vaults** and, on the **Recovery Services vaults** blade, select **+ Add**.
1. On the **Basics** tab of the **Create Recovery Services vault** blade, specify the following settings (leave others with their default values) and select **Review + create**:

   *Table 14: Recovery Services vault (for Azure Backup) settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|select Create new, in the Name text box, enter ws2019-08-rg4 and select OK|
   |Vault name|ws2019-08b-rsvault|
   |Location|the name of an Azure region into which you deployed the Azure VM in the first exercise of this lab|

1. On the **Review + create** tab of the **Create Recovery Services vault** blade, select **Create**:

    > **Note:** By default, the default configuration for Storage Replication type is set to Geo-redundant (GRS) and Soft Delete is enabled. You will change these settings in the lab to simplify deprovisioning, but you should use them in your production environments.

#### Task 2: Configure the Azure Site Recovery vault

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, in the Internet Explorer window displaying the Azure portal, use the **Search resources, services, and docs** text box in the toolbar to search for and select **Recovery Services vaults** and, on the **Recovery Services vaults** blade, select **ws2019-08b-rsvault**.
1. On the **ws2019-08b-rsvault** blade, in the vertical menu, in the **Settings** section, select **Properties**. 
1. On the **ws2019-08b-rsvault | Properties** blade, select the **Update** link under the **Backup Configuration** label.
1. On the **Backup Configuration** blade, set **Storage replication type** to **Locally-redundant**, select **Save** and close the **Backup Configuration** blade.

   > **Note:** Storage replication type cannot be changed once you implement protection.

1. On the **ws2019-08b-rsvault | Properties** blade, select the **Update** link under the **Security Settings** label.
1. On the **Security Settings** blade, set **Soft Delete** to **Disable**, select **Save** and close the **Security Settings** blade.

### Task 3: Install the Azure Recovery Services agent

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, in the Internet Explorer window displaying the Azure portal, on the **ws2019-08b-rsvault** Recovery Services vault blade, in the vertical menu, in the **Getting started** section, select **Backup**.
1. On the **ws2019-08b-rsvault \|Backup** blade, specify the following settings:

   *Table 15: Backup settings*

   |Settings| Value|
   |---|---|
   |Where is your workload running?|On-premises|
   |What do you want to backup?|Files and folders|

   > **Note:** Even though the virtual machine you are using in this task is running in Azure, you can leverage it to evaluate the backup capabilities applicable to any on-premises computer running Windows Server operating system.

1. On the **ws2019-08b-rsvault \|Backup** blade, select **Prepare infrastructure**.
1. On the **Prepare infrastructure** blade, select the **Download Agent for Windows Server or Windows Client** link.
1. When prompted, at the bottom of Internet Explorer window, select **Run** to start installation of **MARSAgentInstaller.exe**. This will start the **Microsoft Azure Recovery Services Agent Setup Wizard**. 
1. On the **Installation Settings** page of the **Microsoft Azure Recovery Services Agent Upgrade Wizard**, accept the default settings and select **Next**.
1. On the **Proxy Configuration** page of the **Microsoft Azure Recovery Services Agent Upgrade Wizard**, accept the default settings, and then select **Next**.
1. On the **Microsoft Update Opt-in** page of the **Microsoft Azure Recovery Services Agent Upgrade Wizard**, select **I do not want to use Windows Update**, and select **Next**.
1. On the **Installation** page of the **Microsoft Azure Recovery Services Agent Upgrade Wizard**, select **Install**.
1. After the installation completes, on the **Installation** page of the **Microsoft Azure Recovery Services Agent Upgrade Wizard**, select **Proceed to Registration**. This will launch the **Register Server Wizard**.
1. Switch to the Internet Explorer window displaying the Azure portal, on the **Prepare infrastructure** blade, select the checkbox **Already downloaded or using the latest Recovery Server Agent**, and select **Download**.
1. When prompted, whether to open or save the vault credentials file, select **Save**. This will save the vault credentials file to the local Downloads folder.
1. Switch back to the **Register Server Wizard** window and, on the **Vault Identification** page, select **Browse**.
1. In the **Select Vault Credentials** dialog box, browse to the **Downloads** folder, in the **Select Vault Credentials** dialog box, navigate to the **Downloads** folder, select the vault credentials file you downloaded, and select **Open**.
1. On the **Encryption Setting** page of the **Register Server Wizard**, select **Generate Passphrase**.
1. On the **Encryption Setting** page of the **Register Server Wizard**, select the **Browse** button next to the **Enter a location to save the passphrase** drop-down list.
1. In the **Browse For Folder** dialog box, expand **This PC** node, select the **Documents** subfolder and select **OK**.
1. Select **Finish**, review the **Microsoft Azure Backup** warning and select **Yes**, and wait for the registration to complete.

   > **Note:** In a production environment, you should store the passphrase file in a secure location other than the server being backed up.

1. On the **Server Registration** page of the **Register Server Wizard**, review the warning regarding the location of the passphrase file, ensure that the **Launch Microsoft Azure Recovery Services Agent** checkbox is selected and select **Close**. This will automatically open the **Microsoft Azure Backup** console.

### Task 4: Schedule Azure Backup

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, in the **Microsoft Azure Backup** console, in the **Actions** pane, select **Schedule Backup**.
1. In the **Schedule Backup Wizard**, on the **Getting started** page, select **Next**.
1. On the **Select Items to Backup** page, select **Add Items**.
1. In the **Select Items** dialog box, navigate to the **C:\\Windows\\System32\\drivers\\etc\\** folder, select **hosts**, and then select **OK**:
1. On the **Select Items to Backup** page, select **Next**.
1. On the **Specify Backup Schedule** page, ensure that the **Day** option is selected, in the first drop-down list box below the **At following times (Maximum allowed is three times a day)** box, select **4:30 AM**, and then select **Next**.
1. On the **Select Retention Policy** page, accept the defaults, and then select **Next**.
1. On the **Choose Initial Backup type** page, accept the defaults, and then select **Next**.
1. On the **Confirmation** page, select **Finish**. When the backup schedule is created, select **Close**.
1. In the **Microsoft Azure Backup** console, in the Actions pane, select **Back Up Now**.

   > **Note:** The option to run backup on demand becomes available once you create a scheduled backup.

1. In the Back Up Now Wizard, on the **Select Backup Item** page, ensure that the **Files and Folders** option is selected and select **Next**.
1. On the **Retain Backup Till** page, accept the default setting and select **Next**.
1. On the **Confirmation** page, select **Back Up**.
1. When the backup is complete, select **Close**.
1. Switch to the Internet Explorer window displaying the Azure portal, navigate back to the **ws2019-08b-rsvault** Recovery Services vault blade and select **Backup items**. 
1. On the **ws2019-08b-rsvault \| Backup items** blade, select the **Azure Backup Agent** entry.
1. On the **Backup Items (Azure Backup Agent)** blade, verify that there is an entry referencing the **C:\\** drive of **ws2019-08-vm1.**.

#### Task 5: Perform file recovery by using Azure Recovery Services agent

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, open File Explorer, navigate to the **C:\\Windows\\System32\\drivers\\etc\\** folder and delete the **hosts** file.
1. Switch to the Microsoft Azure Backup window and select **Recover data**. This will start **Recover Data Wizard**.
1. On the **Getting Started** page of **Recover Data Wizard**, ensue that **This server (ws2019-08-vm1.)** option is selected and select **Next**.
1. On the **Select Recovery Mode** page, ensure that **Individual files and folders** option is selected, and select **Next**.
1. On the **Select Volume and Date** page, in the **Select the volume** drop down list, select **C:\\**, accept the default selection of the available backup, and select **Mount**. 

   > **Note:** Wait for the mount operation to complete. This might take about 2 minutes.

1. On the **Browse And Recover Files** page, note the drive letter of the recovery volume, select **Browse**, and review the tip regarding the use of **Robocopy**. 
1. Select **Start**, expand the **Windows System** folder, and select **Command Prompt**.
1. From the **Administrator: Command Prompt** window, run the following to copy the restore the **hosts** file to the original location (replace `[recovery_volume]` with the drive letter of the recovery volume you identified earlier):

   ```cmd
   robocopy [recovery_volume]:\Windows\System32\drivers\etc C:\Windows\system32\drivers\etc hosts /r:1 /w:1
   ```

1. From the **Administrator: Command Prompt** window, run the following to verify that the file has been restored:

   ```cmd
   dir C:\Windows\system32\drivers\etc\hosts
   ```

1. Switch back to the **Recover Data Wizard** and, on the **Browse and Recover Files**, select **Unmount** and, when prompted to confirm, select **Yes**. 

## Exercise 5: Deprovisioning the Azure lab environment

### Task 1: Remove the protected items

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, switch to the Internet Explorer window displaying the **Backup Items (Azure Backup Agent)** blade of the Azure portal and select the entry referencing the **C:\\** drive of **ws2019-08-vm1.**.
1. On the **C:\\ on ws2019-08-vm1.** blade, select the **ws2019-08-vm1.** link.
1. On the **ws2019-08-vm1.** blade, select **Delete**. 
1. On the **Delete** blade, specify the following information, select the checkbox **There is backup data of 1 backup items associated with this server. I understand that clicking "Confirm" will permanently delete all cloud backup data. This action cannot be undone. An alert may be sent to the administrators of this subscription notifying them of this deletion**, and select **Delete**:

   *Table 16: Backup item delete settings*

   |Settings| Value|
   |---|---|
   |TYPE THE SERVER NAME|ws2019-08-vm1.|
   |Reason|Decommissioned|
   |Comments|Decommissioned|

1. Close the Virtual Machine Connection window to **ws2019-08-vm1**, in the Remote Desktop session to **ws2019-08-hvm0**, in the Internet Explorer displaying the Azure portal, navigate to the **ws2019-08a-rsvault | Replicated items** blade, and select the **ws2019-08-vm1** entry.
1. On the **ws2019-08-vm1** replicated items blade, select the ellipsis in the toolbar and, in the drop-down menu, select **Disable replication**. 
1. On the **Disable replication** blade, ensure that the **Disable replication and remove (Recommended)** entry appears in the **Remove replicated items** drop-down list, select **I don't want to provide feedback** checkbox, and select **OK**.

### Task 2: Delete the lab resource groups

1. On **SEA-CL1**, close the Remote Desktop session to **ws2019-08-hvm0**, switch to the Microsoft Edge window displaying the Azure portal.
1. In the Azure portal, open the **Azure Cloud Shell** pane by selecting the Cloud Shell button in the Azure portal.
1. If prompted to select either **Bash** or **PowerShell**, and then select **PowerShell**.

    > **Note:** If this is the first time you're starting **Cloud Shell** and you're presented with the **You have no storage mounted** message, select the subscription you are using in this lab, and then select **Create storage**.

1. From the Cloud Shell blade, run the following command to delete all resource groups created throughout this lab:

   ```powershell
   Get-AzResourceGroup -Name 'ws2019-08-*' | Remove-AzResourceGroup -Force -AsJob
   ```

   > **Note:** The command executes asynchronously (as determined by the *-AsJob* parameter), so while you will be able to run another PowerShell command immediately after within the same PowerShell session, it will take a few minutes before the resource groups are actually removed.
