---
lab:
    title: 'Lab: Implementing Azure-based recovery services'
    module: 'Module 8: Planning and implementing migration and recovery services in hybrid scenarios'
---

# Lab: Implementing Azure-based recovery services

## Scenario 
To address concerns regarding the outdated operational model, the limited use of automation, and reliance on tape backups for restores and disaster recovery, you decide to use Azure-based recovery services. As the first step, you'll implement Azure Site Recovery and Azure Backup.

## Objectives
After completing this lab, you'll be able to: 

- Implement the lab environment.
- Create and configure an Azure Site Recovery vault.
- Implement Hyper-V VM protection by using Azure Site Recovery vault.
- Implement Azure Backup.
- Deprovision the Azure lab environment.

## Estimated time: 60 minutes

## Lab setup
Virtual machines: SEA-CL1 and SEA-DC1

User name: **Contoso\\Administrator**

Password: **Pa55w.rd**

## Exercise 1: Implementing the lab environment

### Scenario

You need to test a number of Azure VM backup and recovery scenarios. To start, you will provision an Azure VM running Windows Server 2019 with the Hyper-V role installed by using an Azure Quickstart template. You will then install Hyper-V VM running Windows Server 2019 within that Azure VM. 

The main tasks for this exercise are as follows:

1. Deploy an Azure VM running Windows Server 2019 with the Hyper-V role installed.
1. Connect to the Azure VM running the Windows Server 2019 with the Hyper-V role installed.
1. Download a Windows Server 2019 VHD file.
1. Deploy a Windows Server 2019 Hyper-V VM within an Azure VM.

#### Task 1: Deploy an Azure VM running Windows Server 2019 with the Hyper-V role installed

1. On **SEA-CL1**, start Microsoft Edge, navigate to the [Azure portal](https://portal.azure.com), and sign in using a user account with the Owner role in the Azure subscription you will be using in this lab.
1. In the Azure portal, create a resource group with the following settings: 

   *Table 1: Resource group settings*

   |Setting|Value|
   |---|---|
   |Subscription|Use the name of the Azure subscription you will be using in this lab.|
   |Resource group|ws2019-08-rg1|
   |Region|Use the name of an Azure region in which you can provision Azure virtual machines.|

1. On **SEA-CL1**, open another Microsoft Edge tab, navigate to the [301-nested-vms-in-virtual-network Azure QuickStart template](https://aka.ms/301-nested-vms-in-virtual-network) and use initiate a deployment the following settings:

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

   > **Note:** Wait for the deployment to complete. The deployment might take about 10 minutes.

#### Task 2: Connect to the Azure VM running the Windows Server 2019 with the Hyper-V role installed

1. On **SEA-CL1**, from the Microsoft Edge displaying the Azure Portal, navigate to the the **ws2019-08-hvm0 | Networking** blade. 
1. From the **ws2019-08-hvm0 | Networking** blade, create an inbound port rule of the network security group associated with the **ws2019-08-hvm0-nic1** network adapter with the following settings:

   *Table 3: Inbound security rule settings*

   |Setting|Value|
   |---|---|
   |Destination port ranges|3389|
   |Protocol|Any|
   |Name|AllowRDPInBound|

    > **Note:** Make sure that you modify the settings of **ws2019-08-hvm0-nic1**, which has the public IP address assigned to it.

1. In the Azure portal, navigate to the **ws2019-08-hvm0** blade. 
1. From the **ws2019-08-hvm0** blade, connect via Remote Desktop to **ws2019-08-hvm0**. When prompted to authenticate, sign in as **Student** with the **Pa55w.rd1234** password. 

#### Task 3: Download a Windows Server 2019 VHD file

1. Within the Remote Desktop session to **ws2019-08-hvm0**, from the **Server Manager** console, disable the **IE Enhanced Security Configuration** for Administrators. 

1. Within the Remote Desktop session to **ws2019-08-hvm0**, use Internet Explorer to download a VHD file containing the **Windows Server 2019** evaluation image to the **F:\\VHDs** folder from the Microsoft Evaluation Center web site. 

#### Task 4: Deploy a Windows Server 2019 Hyper-V VM within an Azure VM

1. Within the Remote Desktop session to **ws2019-08-hvm0**, start **Hyper-V Manager**. 
1. In the **Hyper-V Manager** console, create a new **Virtual Machine** with the following settings: 

   *Table 4: New virtual machine name and location settings*

   |Setting|Value|
   |---|---|
   |Name|ws2019-08-vm1|
   |Store the virtual machine in a different location|selected|
   |Location|F:\VMs|
   |Generation|Generation 1|
   |Startup memory|2048|
   |Use Dynamic Memory|enabled|
   |Connection|NestedSwitch|
   |Use an existing virtual hard disk|the VHD file you downloaded in the previous task|

1. In the **Hyper-V Manager** console, connect to the newly provisioned Hyper-V VM and initialize the operating system with the default settings. When prompted, set the password of the built-in Administrator account to **Pa55w.rd1234**.
1. Sign in to the newly provisioned Hyper-V VM and run the following Windows PowerShell command to set the computer name:

   ```powershell
   Rename-Computer -NewName 'ws2019-08-vm1' -Restart
   ```

   >**Note**: The command will rename the operating system and restart it.


## Exercise 2: Creating and configuring an Azure Site Recovery vault

### Scenario

In order to implement Azure Site Recovery for the nested Windows Server 2019 VM running in an Azure VM, with Azure as the disaster recovery site, you have to first create and configure an Azure Site Recovery vault.

The main tasks for this exercise are as follows:

1. Create an Azure Site Recovery vault.
1. Configure the Azure Site Recovery vault.

#### Task 1: Create an Azure Site Recovery vault

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Internet Explorer window, navigate to the [Azure portal](https://portal.azure.com), and sign in using a user account with the Owner role in the Azure subscription you will be using in this lab.
1. In the Azure portal, create a Recovery Services vault with the following settings (leave others with their default values):

   *Table 5: Recovery Services vault settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|the name of a new resource group ws2019-08-rg2|
   |Vault name|ws2019-08a-rsvault|
   |Location|the name of an Azure region different from the one into which you deployed the Azure VM in the first exercise of this lab|

    > **Note:** By default, the default configuration for Storage Replication type is set to Geo-redundant (GRS) and Soft Delete is enabled. You will change these settings in the lab to simplify deprovisioning, but you should use them in your production environments.

#### Task 2: Configure the Azure Site Recovery vault

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, navigate to the **ws2019-08a-rsvault** blade.
1. From the **ws2019-08a-rsvault** blade, set **Storage replication type** of the vault to **Locally-redundant** which is in the Recovery Vault's Properties - Backup Configuration blade.

   > **Note:** Storage replication type cannot be changed once you implement protection.

1. From the **ws2019-08a-rsvault** blade, disable **Soft Delete** of the vault which is in the Recovery Vault's Properties - Security Settings blade.


## Exercise 3: Implementing Hyper-V VM protection by using Azure Site Recovery vault

### Scenario

With a test Hyper-V VM and a Recovery Services vault created, you can now proceed to implement Hyper-V VM protection by using Azure Site Recovery. You will perform a test failover and review the settings of the planned and unplanned failover. 

The main tasks for this exercise are as follows:

1. Implement an Azure recovery site.
1. Prepare protection of a Hyper-V virtual machine.
1. Enable replication of a Hyper-V virtual machine.
1. Review Azure VM replication settings.
1. Perform a failover of the Hyper-V virtual machine.


#### Task 1: Implement an Azure recovery site

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, create a virtual network with the following settings (leave others with their default values):

   *Table 6: Virtual network ws2019-08-dr-vnet settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|the name of a new resource group ws2019-08-rg3|
   |Name|ws2019-08-dr-vnet|
   |Region|the name of the Azure region into which you deployed the Recovery Services vault earlier in this lab|
   |IPv4 address space|10.8.0.0/22|

1. Within the new virtual network, create a subnet with the following settings (leave others with their default values):

   *Table 7: Virtual network ws2019-08-dr-vnet subnet subnet0 settings*

   |Setting|Value|
   |---|---|
   |Subnet name|subnet0|
   |Subnet address range|10.8.0.0/24|

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, create another virtual network with the following settings (leave others with their default values):

   *Table 8: Virtual network ws2019-08-test-vnet settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|ws2019-08-rg3|
   |Name|ws2019-08-test-vnet|
   |Region|the name of the Azure region into which you deployed the Recovery Services vault earlier in this lab|
   |IPv4 address space|10.0.0.0/22|

1. Within the new virtual network, create a subnet with the following settings (leave others with their default values):

   *Table 9: Virtual network ws2019-08-test-vnet subnet subnet3 settings*

   |Setting|Value|
   |---|---|
   |Subnet name|subnet3|
   |Subnet address range|10.0.2.0/24|

   > **Note:** This matches the IP address range of the production network and the subnet containing the Hyper-V that needs to be protected. 

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, create a storage account with the following settings (leave others with their default values):

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

#### Task 2: Prepare protection of a Hyper-V virtual machine

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, navigate to the **ws2019-08a-rsvault** Recovery Services vault blade. 

1. On the **ws2019-08a-rsvault** blade, in the vertical menu, start configuration of **Site Recovery**

1. On the **ws2019-08a-rsvault \| Site Recovery** blade, in the **Hyper-V machines to Azure** section, select **1. Prepare infrastructure** and specify the following settings:

   *Table 11: Site Recovery Prepare infrastructure settings*

   |Setting|Value|
   |---|---|
   |Deployment planning completed?|Yes, I have done it|
   |Are you Using System Center VMM to manage Hyper-V hosts|No|
   |Source setting: Hyper-V Site|ws2019-08 Hyper-V site|

1. On the **Source settings** tab of the **Prepare infrastructure** blade, select the **Add Hyper-V server** link. 

1. On the **Add Server** blade, select the **Download** link in step 3 of the procedure for adding on-premises Hyper-V hosts in order to download the Microsoft Azure Site Recovery Provider.

1. Install **AzureSiteRecoveryProvider.exe** with **Microsoft Update** option disabled. 

1. From the Azure portal, download the vault registration key into the **Downloads** folder.

1. Complete the **Provider installation** wizard and start the **Microsoft Azure Site Recovery Registration Wizard**.

1. When prompted, in the **Microsoft Azure Site Recovery Registration Wizard**, provide the location of the vault credentials file.

1. Complete the **Microsoft Azure Site Recovery Registration Wizard** with the default settings. 

1. Refresh the browser page displaying the Azure portal and repeat the initial steps of the **1. Prepare infrastructure** procedure.

1. Once you reach the **Source settings** tab of the **Prepare infrastructure** blade, verify that the **Hyper-V site** and **Hyper-V servers** settings are set correctly and continue to the next step. 

1. On the **Target settings** tab of the **Prepare infrastructure** blade, accept the default settings.

1. On the **Replication policy** tab of the **Prepare infrastructure** blade, create a new policy with the following settings and associate it with the the Hyper-V site:

   *Table 12: Policy settings*

   |Setting|Value|
   |---|---|
   |Name|ws2019-08 replication policy|
   |Copy frequency|30 seconds|

1. Complete the **Prepare infrastructure** procedure and wait until the association process completes

#### Task 3: Enable replication of a Hyper-V virtual machine

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Azure portal, on the **ws2019-08a-rsvault \| Site Recovery** blade, in the **Hyper-V machines to Azure** section, select **2. Enable replication**. 
1. On the **Source environment** tab of the **Enable replication** blade, in the **Source location** drop-down list, select **ws2019-08 Hyper-V site**.
1. On the **Target environment** tab of the **Enable replication** blade, specify the following settings (leave others with their default values):

   *Table 13: Target environment settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Post-failover resource group|ws2019-08-rg3|
   |Post-failover deployment model|Resource Manager|
   |Storage account|the name of the storage account you created in the first task of this exercise|
   |Azure network|Configure now for selected machines|
   |Virtual network|ws2019-08-dr-vnet|
   |Subnet|subnet0 (10.8.0.0/24)|

1. On the **Virtual machine selection** tab of the **Enable replication** blade, select the **ws2019-08-vm1** entry.
1. On the **Replication settings** tab of the **Enable replication** blade, set the **Defaults** and **OS type** to **Windows**.
1. Complete the **Enable replication** procedure with the default settings.

#### Task 4: Review Azure VM replication settings

1. In the Azure portal, back on the **ws2019-08a-rsvault \| Site Recovery** blade, in navigate to the the **ws2019-08a-rsvault \| Replicated items** blade.
1. On the **ws2019-08a-rsvault \| Replicated items** blade, ensure that there is an entry representing the **ws2019-08-vm1** virtual machine and verify that its **Replication Health** is listed as **Healthy** and that its **Status** is listed as either **Enabling protection** or displaying a current percentage of synchronization progress.

   > **Note:** You might need to wait a few minutes until the **ws2019-08-vm1** entry appears on the **ws2019-08a-rsvault \| Replicated items** blade.

1. From the **ws2019-08a-rsvault \| Replicated items** blade, navigate to the **ws2019-08-vm1** replicated items blade.
1. On the **ws2019-08-vm1** replicated items blade, review the **Health and status**, **Failover readiness**, **Latest recovery points**, and **Infrastructure view** sections. Note the **Planned Failover**, **Failover** and **Test Failover** toolbar icons.

   > **Note:** Wait until the status changes to **Protected**. This might take additional 15 minutes. You will need to refresh the browser page for the status to be updated. While waiting for the replication of the nested VM to complete, proceed to Exercise 4.

1. On the **ws2019-08-vm1** replicated items blade, select **Latest recovery points** and review **Latest crash-consistent** and **Latest app-consistent** recovery points. 

#### Task 5: Perform a failover of the Hyper-V virtual machine

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the browser window displaying the Azure portal, on the **ws2019-08-vm1** replicated items blade, initiate **Test failover** with the following settings (leave others with their default values) and select **OK**:

   *Table 14: Test failover settings*

   |Setting|Value|
   |---|---|
   |Choose a recovery point|the default option|
   |Azure virtual network|ws2019-08-test-vnet|

1. In the Azure portal, navigate back to the **ws2019-08a-rsvault** blade and, from there, navigate to the listing of **Site Recovery jobs**. Wait until the status of the **Test failover** job is listed as **Successful**.
1. In the Azure portal, navigate to the **Virtual machines** blade and note the entry representing the newly provisioned virtual machine **ws2019-08-vm1-test**.
1. In the Azure portal, navigate back to the **ws2019-08-vm1** replicated item blade and initiate **Cleanup test failover**.
1. Once the test failover cleanup job completes, refresh the browser page displaying the **ws2019-08-vm1** replicated items blade and note that you have the option to perform planned and unplanned failover.
1. From the **ws2019-08-vm1** replicated items blade, navigate to the **Planned failover** blade.
1. On the **Planned failover** blade, note that the failover direction settings are already set and not modifiable. 
1. Close the **Planned failover** blade without initiating a failover. 
1. From the **ws2019-08-vm1** replicated items blade, navigate to the **Failover** blade.
1. On the **Failover** blade, note that you have the option to choose a recovery point. 
1. Close the **Failover** blade without initiating a failover.


## Exercise 4: Implementing Azure Backup

### Scenario

While waiting for the replication of the nested VM to complete, implement Azure Backup of the second Azure VM by using an Azure VM agent and Azure VM-level backup of the third Azure VM.


The main tasks for this exercise are as follows:

1. Create an Azure Site Recovery vault.
1. Configure the Azure Site Recovery vault.
1. Install the Azure Recovery Services agent.
1. Schedule Azure Backup.
1. Perform file recovery by using Azure Recovery Services agent.

#### Task 1: Create an Azure Site Recovery vault

> **Note:** While, in general, the same vault can be used to implement Azure Site Recovery and Azure Backup functionality, the one hosting Azure Backup should be located close to the location of the backed up items. For this reason, you will create another Azure Site Recovery vault in the same Azure region as the Azure VM you deployed in the second exercise of this lab. 

1. Within the Remote Desktop session to **ws2019-08-hvm0**, switch to the Virtual Machine Connection window to **ws2019-08-vm1**.
1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, from the **Server Manager** console, disable the **IE Enhanced Security Configuration** for Administrators. 
1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, in the Internet Explorer window, navigate to the [Azure portal](https://portal.azure.com), and sign in using a user account with the Owner role in the Azure subscription you will be using in this lab.
1. In the Azure portal, create a Recovery Services vault with the following settings (leave others with their default values):

   *Table 15: Recovery Services vault settings*

   |Setting|Value|
   |---|---|
   |Subscription|the name of the Azure subscription you are using in this lab|
   |Resource group|the name of a new resource group ws2019-08-rg4|
   |Vault name|ws2019-08b-rsvault|
   |Location|the name of an Azure region into which you deployed the Azure VM in the first exercise of this lab|

    > **Note:** By default, the default configuration for Storage Replication type is set to Geo-redundant (GRS) and Soft Delete is enabled. You will change these settings in the lab to simplify deprovisioning, but you should use them in your production environments.

#### Task 2: Configure the Azure Site Recovery vault

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, in the Azure portal, navigate to the **ws2019-08b-rsvault** blade.
1. From the **ws2019-08b-rsvault** blade, set **Storage replication type** of the vault to **Locally-redundant**.

   > **Note:** Storage replication type cannot be changed once you implement protection.

1. From the **ws2019-08b-rsvault** blade, disable **Soft Delete** of the vault.

### Task 3: Install the Azure Recovery Services agent

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, in the Internet Explorer window displaying the Azure portal, on the **ws2019-08b-rsvault** Recovery Services vault blade, initiate **Backup** configuration with the following settings:

   *Table 16: Backup settings*

   |Settings| Value|
   |---|---|
   |Where is your workload running?|On-premises|
   |What do you want to backup?|Files and folders|

   > **Note:** Even though the virtual machine you are using in this task is running in Azure, you can leverage it to evaluate the backup capabilities applicable to any on-premises computer running Windows Server operating system.

1. From the **ws2019-08b-rsvault \|Backup** blade, initiate the **Prepare infrastructure** procedure.
1. From the **Prepare infrastructure** blade, download Azure Recovery Services Agent, start the **Microsoft Azure Recovery Services Agent Setup Wizard**, disable the Microsoft Updates option, and complete the installation with the default settings. 
1. After the installation completes, start the **Register Server Wizard**.
1. Switch to the Internet Explorer window displaying the Azure portal and, from the **Prepare infrastructure** blade, download the vault credentials file to the local Downloads folder.
1. Switch back to the **Register Server Wizard** window and, when prompted to provide Vault Credentials, point to the newly downloaded file. 
1. On the **Encryption Setting** page of the **Register Server Wizard**, generate passphrase and store it in the local **Documents** folder. 
1. Review the **Microsoft Azure Backup** warning and proceed to complete the registration. This will automatically open the **Microsoft Azure Backup** console.

   > **Note:** In a production environment, you should store the passphrase file in a secure location other than the server being backed up.


### Task 4: Schedule Azure Backup

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, in the **Microsoft Azure Backup** console, schedule backup with the following settings (leave others with their default values):

   *Table 17: Scheduled backup settings*

   |Settings| Value|
   |---|---|
   |Items to back up|C:\\Windows\\System32\\drivers\\etc\\hosts|
   |Backup Schedule|Daily at 4:30 AM|
   |Retention Policy|default|
   |Initial Backup type|default|

1. In the **Microsoft Azure Backup** console, initiate an on-demand backup with the default settings.

   > **Note:** The option to run backup on demand becomes available once you create a scheduled backup.

1. Switch to the Internet Explorer window displaying the Azure portal, navigate back to the **ws2019-08b-rsvault** Recovery Services vault blade and display **Backup items**. 
1. From the **ws2019-08b-rsvault \| Backup items** blade, navigate to the **Backup Items (Azure Backup Agent)** blade and verify that there is an entry referencing the **C:\\** drive of **ws2019-08-vm1.**.

#### Task 5: Perform file recovery by using Azure Recovery Services agent

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, open File Explorer, navigate to the **C:\\Windows\\System32\\drivers\\etc\\** folder and delete the **hosts** file.
1. Switch to the Microsoft Azure Backup window and start **Recover Data Wizard** with the following settings (leave others with their default values):

   *Table 18: Restore settings*

   |Settings| Value|
   |---|---|
   |Restore target|This server (ws2019-08-vm1.)|
   |Restore items|Individual files and folders|
   |Select the volume|C:\\|

   > **Note:** Wait for the mount operation to complete. This might take about 2 minutes.

1. On the **Browse And Recover Files** page, note the drive letter of the recovery volume, select **Browse**, and review the tip regarding the use of **Robocopy**. 
1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, start **Command Prompt**.
1. From the **Administrator: Command Prompt** window, run the following to copy the restore the **hosts** file to the original location (replace `[recovery_volume]` with the drive letter of the recovery volume you identified earlier):

   ```cmd
   robocopy [recovery_volume]:\Windows\System32\drivers\etc C:\Windows\system32\drivers\etc hosts /r:1 /w:1
   ```

1. From the **Administrator: Command Prompt** window, run the following to verify that the file has been restored:

   ```cmd
   dir C:\Windows\system32\drivers\etc\hosts
   ```

1. Switch back to the **Recover Data Wizard** and unmount the mounted backup file. 


## Exercise 5: Deprovisioning the Azure lab environment

### Scenario

To minimize Azure-related charges, you want to deprovision the Azure resources provisioned throughout this lab.

The main tasks for this exercise are as follows:

1. Remove the protected items.
1. Delete the lab resource groups.

### Task 1: Remove the protected items

1. Within the Remote Desktop session to **ws2019-08-hvm0**, in the Virtual Machine Connection window to **ws2019-08-vm1**, switch to the Internet Explorer window displaying the **Backup Items (Azure Backup Agent)** blade of the Azure portal and select the entry referencing the **C:\\** drive of **ws2019-08-vm1.**.
1. On the **C:\\ on ws2019-08-vm1.** blade, navigate to the **ws2019-08-vm1.** blade.
1. From the **ws2019-08-vm1.** blade, specify the following information and delete the backup: 

   *Table 19: Backup item delete settings*

   |Settings| Value|
   |---|---|
   |TYPE THE SERVER NAME|ws2019-08-vm1.|
   |Reason|Decommissioned|
   |Comments|Decommissioned|

1. Close the Virtual Machine Connection window to **ws2019-08-vm1**, in the Remote Desktop session to **ws2019-08-hvm0**, in the Internet Explorer displaying the Azure portal, navigate to the **ws2019-08a-rsvault | Replicated items** blade, and select the **ws2019-08-vm1** entry.
1. From the **ws2019-08-vm1** replicated items blade, disable replication and remove remove replicated items without providing feedback. 

### Task 2: Delete the lab resource groups

1. On **SEA-CL1**, close the Remote Desktop session to **ws2019-08-hvm0**, switch to the Microsoft Edge window displaying the Azure portal.
1. In the Azure portal, open a PowerShell session in the **Azure Cloud Shell** pane.

    > **Note:** If this is the first time you're starting **Cloud Shell** and you're presented with the **You have no storage mounted** message, select the subscription you are using in this lab, and then select **Create storage**.

1. From the Cloud Shell blade, run the following command to delete all resource groups created in this lab:

   ```powershell
   Get-AzResourceGroup -Name 'ws2019-08-*' | Remove-AzResourceGroup -Force -AsJob
   ```

   > **Note:** The command executes asynchronously (as determined by the *-AsJob* parameter), so while you will be able to run another PowerShell command immediately after within the same PowerShell session, it will take a few minutes before the resource groups are actually removed.
