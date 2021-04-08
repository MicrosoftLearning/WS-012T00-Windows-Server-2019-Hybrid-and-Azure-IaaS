---
lab:
    title: 'Lab: Implementing Azure File Sync'
    module: 'Module 5: Implementing File Services in Hybrid Scenarios'
---

# Lab: Implementing Azure File Sync

## Scenario

To address concerns regarding Distributed File System (DFS) Replication between Contoso's London headquarters and its Seattle–based branch office, you decide to test Azure File Sync as an alternative replication mechanism between two on-premises file shares.

## Objectives

After completing this lab, you'll be able to:

- Implement DFS Replication in your on-premises environment.
- Create and configure a sync group.
- Replace DFS Replication with Azure File Sync based replication.
- Verify replication and enable cloud tiering.
- Troubleshoot replication conflicts.

## Estimated time: 45 minutes

## Lab setup

Virtual machines: **LON-SVR1**, **SEA-CL1**, **SEA-DC1**, **SEA-SVR1**, and **SEA-SVR2**

User name: **Contoso\\Administrator**

Password: **Pa55w.rd**

## Exercise 1: Implementing DFS Replication in your on-premises environment

### Scenario

Exercise scenario: Before you start testing an on-premises DFS Replication migration, you first need to implement DFS Replication in your proof-of-concept environment on **LON-SVR1** and **SEA-SVR1**.

The main tasks for this exercise are:

1. Deploy DFS.
2. Test DFS deployment.

### Task 1: Deploy DFS

1. Sign in to **SEA-CL1** as **Contoso\\Administrator** and use **Pa55w.rd** as the password. If in the Lab Setup the **Allfiles** folder was copied to the **Desktop**, then open the **Allfiles** folder, and copy the **Labfiles** folder to **C:**.  Then share the **Labfiles** folder to **Everyone** with **Read** access.
2. In File Explorer, browse to the C:\Labfiles\Mod05 folder, and then run **M05-DeployDFS.ps1**.

### Task 2: Test DFS deployment

1. On **SEA-CL1**, run **DFS Management**, and then add the ```\\Contoso.com\Root\``` namespace and the **Branch1** replication group to display.
2. Verify that the ```\\Contoso.com\Root\Data``` folder has targets on **LON-SVR1** and **SEA-SVR1**. Note which folders are configured as the targets.
3. Verify that the **Branch1** replication group has two members, **LON-SVR1** and **SEA-SVR1**. Note which folders are replicated on each server.
4. Open two instances of File Explorer. In the first **File Explorer** window, connect to **\\\\LON-SVR1\\Data**, and then in the second **File Explorer** window, connect to **\\\\SEA-SVR1\\Data**.

    >**Note:** Wait until the files are replicated and both the File Explorer windows record the same content.

5. Create a new file with your name in **\\\\LON-SVR1\\Data**, and then confirm that the file replicates to **\\\\SEA-SVR1\\Data** after a few seconds. This confirms that DFS Replication is working.

### Results

After completing this exercise, you have a working DFS infrastructure. This includes DFS Replication, which replicates content between **LON-SVR1** and **SEA-SVR1**.

## Exercise 2: Creating and configuring a sync group

### Scenario

To prepare for migrating the DFS Replication environment to File Sync, you must first create and configure a File Sync group.

The main tasks for this exercise are:

1. Create an Azure file share.
2. Use an Azure file share.
3. Deploy Storage Sync Service and a File Sync group.

### Task 1: Create an Azure file share

1. On **SEA-CL1**, open the Azure portal, and then authenticate with your Azure credentials.
2. In the Azure portal, create an Azure storage account in a resource group named **RG1**. You can use the default settings, but make sure that the storage account has a unique name. For example, you can specify the storage account name in the following format:\<*YourLowercaseInitials*>*DDMMYY*; for example, **df150620** if your name is Devon Torres and you're creating the storage account on June 15, 2020. If that name is already taken, add another character to the name until the name is available.

    >**Note:** Use the same region for deploying all resources in this lab.

3. In the storage account, create a file share named **share1**.

### Task 2: Use an Azure file share

1. On SEA-CL1, upload the C:\Labfiles\Mod05\File1.txt file to share1.
2. In the Azure portal, create a snapshot of **share1**.
3. On **SEA-CL1**, mount **share1** to drive **Z** by using the connection script that the Azure portal provides.
4. In File Explorer, on the mounted drive, open the file named **File1.txt**, enter your name, and then save the file.
5. Use **Previous Versions** in File Explorer to restore the previous version of **File1.txt**.
6. Open **File1.txt**, and then verify that it doesn't include your name.

### Task 3: Deploy Storage Sync Service and a File Sync group

1. On **SEA-CL1**, use the Azure portal to create an Azure File Sync resource named **FileSync1**. Use the same region and Resource Group as you used when deploying the storage account.

    >**Note:** Deploying File Sync creates a Storage Sync Service resource.

2. Create a sync group named **Sync1** in the **FileSync1** Storage Sync Service. Use the storage account that you created earlier and **share1** as the Azure file share when creating **Sync1**.
3. Verify that no server is currently registered with **FileSync1**.

### Results

After completing this exercise, you have a File Sync group. You also have the cloud endpoint mapped on **SEA-CL1** so that you can inspect the Azure file share content.

## Exercise 3: Replacing DFS Replication with File Sync&ndash;based replication

### Scenario

Now that you have all the necessary components in place, it's time to replace DFS Replication with File Sync&ndash;based replication.

The main tasks for this exercise are:

1. Add **SEA-SVR1** as a server endpoint.
2. Register **LON-SVR1** with File Sync.
3. Remove DFS Replication and add **LON-SVR1** as a server endpoint.

### Task 1: Add SEA-SVR1 as a server endpoint

1. On **SEA-CL1**, in the Azure portal, download the File Sync agent for Windows Server 2019 (**StorageSyncAgent_WS2019.msi**), and then save it to the **C:\\Labfiles** folder.

    >**Note:** If you downloaded the file to the default location, you need to copy the file to the **C:\\Labfiles** folder.

2. Open the **C:\\Labfiles\\Mod05\\Install-FileSyncServerCore.ps1** file, update the value of the *$RG_name* variable with the name of the resource group to which you deployed **FileSync1** (replace everything inside \<>, including \< and >, but leave the apostrophe at the beginning and at the end), and then save the file.
3. Run **C:\\Labfiles\\Mod05\\Install-FileSyncServerCore.ps1**. This script installs the File Sync agent on the remote Windows Server.
4. Wait while the script is running&mdash;it takes some time. When you get the **WARNING** output, copy the nine-character code in the warning output to the Clipboard.
5. In a new Microsoft Edge tab, browse to **https://microsoft.com/devicelogin**.
6. In Microsoft Edge, paste the code in the **Enter code** dialog box, sign in with your Azure credentials, and then close the Microsoft Edge tab.
7. In the Azure portal, refresh the registered servers in the **FileSync1** Storage Sync Service, and then point out that ```SEA-SVR1.Contoso.com``` is now registered.
8. In File Explorer, open **\\\\SEA-SVR1\\Data**, and then point out that the folder doesn't contain **File1.txt**.
9. Use the Azure portal to add **D:\\Data** on ```SEA-SVR1.Contoso.com``` as a server endpoint to **Sync1**.
10. Use File Explorer to verify that **File1.txt** is available on **\\\\SEA-SVR1\\Data\\**.

    >**Note:** You uploaded **File1.txt** to the **File1.txtAzure** file share, from where it was synced to **SEA-SVR1** by File Sync.

### Task 2: Register LON-SVR1 with File Sync

1. On **SEA-CL1**, in the **C:\\Labfiles\\Mod05\\Install-FileSyncServerCore.ps1** file, for the **$Server** variable, replace **SEA-SVR1** with **LON-SVR1**, and then save the file.
2. Run **C:\\Labfiles\\Mod05\\Install-FileSyncServerCore.ps1**.
3. Wait while the script is running&mdash;it takes some time. When you get the **WARNING** output, copy the nine-character code in the warning output to the Clipboard.
4. In a new Microsoft Edge tab, browse to **https://microsoft.com/devicelogin**.
5. In Microsoft Edge, paste the code in the **Enter code** dialog box, sign in with your Azure credentials, and then close the Microsoft Edge tab.
6. Use the Azure portal to verify that ```SEA-SVR1.Contoso.com``` and ```LON-SVR1.Contoso.com``` are registered with the **FileSync1** Storage Sync Service.

### Task 3: Remove DFS Replication and add LON-SVR1 as a server endpoint

1. On **SEA-CL1**, use **DFS Management** to delete the **Branch1** replication group.
2. Use the Azure portal to add **D:\Data** on ```LON-SVR1.Contoso.com``` as a server endpoint to **Sync1**.

### Results

After completing this exercise, you'll have replaced DFS Replication with File Sync.

## Exercise 4: Verifying replication and enabling cloud tiering

### Scenario

Exercise scenario: You now need to verify that you have successfully replaced DFS Replication with File Sync, and after confirming this, you need to enable cloud tiering.

The main tasks for this exercise are:

1. Verify File Sync.
2. Enable cloud tiering.

### Task 1: Verify File Sync

1. On **SEA-CL1**, use two instances of File Explorer. In the first File Explorer instance, connect to **\\\\LON-SVR1\\Data**, and in the second File Explorer instance, connect to **\\\\SEA-SVR1\\Data**.
2. Create a file with your last name in the **\\\\LON-SVR1\\Data** folder.
3. Verify that after some time, the file with your last name also appears in the **\\\\SEA-SVR1\\Data** folder.

>**Note** You removed DFS Replication in the previous exercise. File Sync replicated the file with your name.

### Task 2: Enable cloud tiering

1. On **SEA-CL1**, use the Azure portal to browse to the **Sync1** sync group in the **FileSync1** Storage Sync Service.
2. In the Azure portal, enable cloud tiering for the ```LON-SVR1.Contoso.com``` endpoint in **Sync1**. Set the **free disk space** policy to **80** percent and the **date policy** to cache files that were accessed in last **7** days.
3. In the File Explorer instance that's connected to the **\\\\LON-SVR1\\Data** folder, in the **details** pane, add the **Attributes** column by right-clicking or accessing the context menu for the **Title** column; for example, on **Name**, select **More**, and then select **Attributes**.

    >**Note:** After some time, the files on **LON-SVR1** would automatically tier. You can trigger tiering immediately by running on LON-Cl1:
    ```Terminal-nocopy
    Enter-PSSession -computername lon-svr1 
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll" 
    Invoke-StorageSyncCloudTiering -Path D:\data 
    ```

4. Verify the attributes of the files. Additionally, verify the size on the disk for the **Windows Server 2016 Hybrid Cloud.pdf** file by reviewing its properties.

### Results

After completing this exercise, you'll have a working File Sync replication and a configured cloud tiering.

## Exercise 5: Troubleshooting replication issues

### Scenario

Exercise scenario: Contoso relies heavily on its DFS Replication implementation. You must ensure that any replication issues, including replication conflicts, can be quickly identified and resolved. To do so, you'll simulate the most common replication issues in your proof-of-concept environment and test their resolutions.

The main tasks for this exercise are:

1. Monitor File Sync replication.
2. Test replication conflict resolution.

### Task 1: Monitor File Sync replication

1. On **SEA-CL1**, use File Explorer to copy the **C:\\Windows\\INF** folder to **\\\\LON-SVR1\Data\\**. The folder will sync to the cloud endpoint, causing sync traffic.
2. In the Azure portal, browse to the **Sync1** sync group in the **FileSync1** Storage Sync Service.
3. In the **Server endpoint** section, verify that the **Health** of both endpoints has green check marks.
4. Select the ```LON-SVR1.Contoso.com``` endpoint, in the **Server Endpoint Properties** pane, review **Sync Activity**, and then close the pane.
5. Select the **Files Synced** graph, and then explore how you can customize the graph by using a filter.
6. Verify if the **INF** folder is syncing to drive **Z**.
7. In the Azure portal, verify that the **INF** sync traffic is visible in the **Files Synced** and **Bytes Synced** graphs. The **INF** folder has more than 800 files, and its size is more than 40 megabytes (MB).

### Task 2: Test replication conflict resolution

1. On **SEA-CL1**, in File Explorer, verify that **File1.txt** is available on **\\\\LON-SVR1\Data\\**. Remember that you uploaded **File1.txt** to the Azure file share, from where it was synced to **SEA-SVR1** by File Sync.
2. Create a file named **Sync.txt** on **\\\\LON-SVR1\\Data\\**.
3. In File Explorer, verify that the **Sync.txt** file is also on drive **Z**. Remember that drive **Z** is the mounted Azure file share, where the file was synced from **LON-SVR1** by File Sync.
4. Create a file named **Demo.txt** on drive **Z**.
5. Create a file named **Demo.txt** on **\\\\LON-SVR1\\Data\\**.

    >**Note:** You're creating files with the same name to cause a sync conflict.

6. In File Explorer, review the content of drive **Z**. Verify that drive **Z**, which is the mounted Azure file share, consists of the **Demo.txt** and **Demo-LON-SVR1.txt** (or **Demo-Cloud.txt**) files. This is because File Sync detected sync conflict and added the endpoint name (**LON-SVR1**) to the file that caused the conflict.

    >**Note:** You might need to wait up to a minute for the sync conflict to occur and for both files to appear on drive **Z**.

### Results

After completing this exercise, you'll monitor File Sync replication and resolve replication conflicts.

## Exercise 6: Cleaning up the Azure subscription

Exercise scenario: To minimize Azure-related charges, you will clean up the Azure subscription.

### Task 1: Delete the Azure resources that were created in the lab

1. On **SEA-CL1**, use the Azure portal to browse to the **FileSync1** Storage Sync Service.
1. Remove ```LON-SVR1.Contoso.com``` and ```SEA-SVR1.Contoso.com``` as registered servers.
1. Delete the **share1** cloud endpoint in the **Sync1** sync group.
1. Delete the **Sync1** sync group.
1. Delete the **FileSync1** Storage Sync Service and the Azure storage account that you created in the lab (the storage account has a name in the \<*YourLowercaseInitials*>*DDMMYY* format).
1. Delete the **RG1** resource group.

### Results

After completing this exercise, you'll clean up the Azure resources that were created in the lab.
