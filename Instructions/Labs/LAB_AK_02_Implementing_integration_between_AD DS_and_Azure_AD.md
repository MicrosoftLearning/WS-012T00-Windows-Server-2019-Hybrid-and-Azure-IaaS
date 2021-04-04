---
lab:
    title: 'Lab: Implementing integration between AD DS and Azure AD'
    type: 'Answer Key'
    module: 'Module 2: Implementing Identity in Hybrid Scenarios'
---

# Lab: Implementing integration between AD DS and Azure AD

## Exercise 1: Preparing Azure AD for AD DS integration

### Task 1: Create a custom domain in Azure

1. On **SEA-SVR2**, start Microsoft Edge, and then browse to the Microsoft Azure portal.

1. Use the credentials that your instructor provides to sign in to the Azure portal.

1. In the Azure portal, browse to **Azure Active Directory**.

1. On the **Azure Active Directory** page, select **Custom domain names**.

1. On the **Custom domain names** page, select **Add custom domain**.

1. On the **Custom domain name** pane, in the **Custom domain name** text box, enter ```contoso.com```, and then select **Add domain**.

1. On the ```Contoso.com``` custom domain name page, review the Domain Name System (DNS) record types that you would use to verify the domain.

1. Close the pane without verifying the domain name.

>**Note:** The domain name provided might not be a valid domain. While you would use DNS records to verify a domain, this lab doesn't require that verification step.

### Task 2: Create a user with the Global Administrator role

1. On **SEA-SVR2**, on the **Azure Active Directory** page in the Azure portal, select **Users**.

1. On the **All Users** page, select **New User**.

1. On the **New User** page, under **Identity**, in the **User name** and **Name** text boxes, enter **admin**.

     >**Note:** Ensure the domain name drop-down menu for the **User name** lists the default domain name ending with ```onmicrosoft.com```.

1. Under **Password**, select the **Show Password** check box. Make note of the password as you'll use it later.

1. Under **Groups and roles**, next to **Roles**, select **User**.

1. On the **Directory roles** page, from the list of roles, select **Global administrator**, and then select **Select**.

1. On the **New user** page, select **Create**.

### Task 3: Reset the password for the user with the Global Administrator role

1. On the Azure portal, select your user account, and then select **Sign out**.

1. On the **Pick an account** page, select **Use another account**.

1. On the **Sign in** page, enter the fully-qualified username of the user account you previously created, and then select **Next**.

1. For the current password, use the password that you wrote down in the previous step.

1. Enter **Pa55w.rdPa55w.rd** as the new password, and then select **Sign in**.

>**Note:** The Azure portal might not allow using the shorter password when updating.

## Exercise 2: Preparing on-premises AD DS for Azure AD integration

### Task 1: Install IdFix

1. On **SEA-SVR2**, open Microsoft Edge, and then browse to **https://github.com/microsoft/idfix**.

1. On the **Github** page, under **ClickOnce Launch**, select **launch**.

1. On the status bar, select **Open file**.

1. In the **Application Install - Security Warning** dialog box, select **Install**.

1. In the **IdFix Privacy Statement** dialog box, review the disclaimer, and then select **OK**.

### Task 2: Run IdFix

1. In the **IdFix** window, select **Query**.

1. Review the list of objects from Active Directory, and observe the **ERROR** and **ATTRIBUTE** columns. In this scenario, the value of **displayName** for **ContosoAdmin** is blank, and the tool's recommended new value appears in the attribute column.

1. In the **IdFix** window, from the **ACTION** drop-down menu, select **Edit**, and then select **Apply** to automatically implement the recommended changes.

1. In the **Apply Pending** dialog box, select **Yes**.

1. Close the IdFix tool.

## Exercise 3: Downloading, installing, and configuring Azure AD Connect

### Task 1: Install and configure Azure AD Connect

1. On **SEA-SVR2**, open Microsoft Edge, browse to the Microsoft website, and search for “Install Azure AD Connect” to find the **Microsoft Azure Active Directory Connect** page.

1. On the **Microsoft Azure Active Directory Connect** page, select **Download**.

1. On the status bar, select **Open file**.

1. On the **Microsoft Azure Active Directory Connect** page, select the **I agree to the license terms and privacy notice** check box, and then select **Continue**.

1. On the **Express Settings** page, select **Use express settings**.

1. On the **Connect to Azure AD** page, enter the username and password of the Global Administrator account you created in exercise 1, then select **Next**.

1. On the **Connect to AD DS** page, enter the following credentials, and then select **Next**:
   - Username: **CONTOSO\\Administrator**
   - Password: **Pa55w.rd**

1. On the **Azure AD sign-in configuration** page, verify that the new domain you added is in the list of Active Directory UPN Suffixes.

     >**Note:** The domain name provided might not be a verified domain. While you typically must verify a domain prior to installing Azure AD Connect, this lab doesn't require that verification step.

1. Select the **Continue without matching all UPN suffixes to verified domains** check box, and then select **Next**.

1. On the **Ready to configure** page, review the list of actions, and then select **Install**.

1. On the **Configuration complete** page, select **Exit**.

## Exercise 4: Verifying integration between AD DS and Azure AD

### Task 1: Verify synchronization in the Azure portal

1. On **SEA-SVR2**, start Microsoft Edge, and then browse to the Azure portal.

1. Sign in to the Azure portal by using the credentials for the account you created in exercise 1.

1. In the Azure portal, open **Azure Active Directory**.

1. On the **Azure Active Directory** page, select **Azure AD Connect**.

1. On the **Azure AD Connect** page, review the information under **Provision from Active Directory**.

1. On the **Azure Active Directory** page, select **Users**.

1. Observe the list of users that synced from the on-premises Active Directory.

     >**Note:** When you begin directory synchronization, it can take 15 minutes for Active Directory objects to appear in the Azure AD portal.

1. In Microsoft Edge, select the **Back** button.

1. On the **Azure Active Directory** page, select **Groups**.

1. Observe the list of groups that synced from the on-premises Active Directory.

### Task 2: Verify synchronization in the Synchronization Service Manager

1. On **SEA-SVR2**, on the **Start** menu, expand **Azure AD Connect**, and then select **Synchronization Service**.

1. In the **Synchronization Service Manager** window, under the **Operations** tab, observe the tasks that were performed to sync the Active Directory objects.

1. Select the **Connectors** tab and observe the two connectors.

     >**Note:** One connector is for the on-premises Active Directory and the other is for the Azure domain.

1. Close the **Synchronization Service Manager** window.

### Task 3: Update a user account in Active Directory

1. On **SEA-SVR2**, from **Server Manager**, open **Active Directory Users and Computers**.

1. In **Active Directory Users and Computers**, expand the **Sales** organizational unit (OU), and then open the properties for **Ben Miller**.

1. In the properties of the user, select the **Organization** tab.
1. In the **Job Title** text box, enter **Manager**, and then select **OK**.

### Task 4: Create a user account in Active Directory

1. In **Active Directory Users and Computers**, right-click or access the context menu for the **Sales** OU, select **New**, and then select **User**.

1. In the **New Object - User** window, enter the following user details for each field, and then select **Next**:

   - First name: **Jordan**
   - Last name: **Mitchell**
   - User logon name: **Jordan**

1. In the **Password** and **Confirm password** fields, enter **Pa55w.rd**, and then select **Next**.

1. Select **Finish**.

### Task 5: Sync changes to Azure AD

1. On **SEA-SVR2**, on the **Start** menu, select **Windows PowerShell**.

1. In the **Windows PowerShell** window, enter the following, and then select Enter:

    ```powershell
    Start-ADSyncSyncCycle
    ```

>**Note:** When you begin directory synchronization, it can take 15 minutes for Active Directory objects to appear in the Azure AD portal

### Task 6: Verify changes in Azure AD

1. On **SEA-SVR2**, start Microsoft Edge, and then browse to the Azure portal.

1. On the **Azure Active Directory** page, select **Users**.

1. On the **All Users** page, search for the user **Ben**.

1. Open the properties page of the user **Ben Miller**, and then verify the **Job title** attribute that synced from Active Directory.

1. In Microsoft Edge, select the **Back** button.

1. On the **All Users** page, search for the user **Jordan**.

1. Open the properties page of the user **Jordan Mitchell**, and then verify the attributes that synced from Active Directory.

1. In Microsoft Edge, select the **Back** button twice.

## Exercise 5: Implementing Azure AD integration features in AD DS

### Task 1: Enable self-service password reset in Azure

1. On **SEA-SVR2**, in the Azure portal, on the **Azure Active Directory** page, select **Password reset**.

1. On the **Password reset** page, select **Get a free Premium trial to use this feature**.

1. On the **Activate** page, under **AZURE AD PREMIUM P2**, select **Free trial**, and then select **Activate**.

1. Right-click or access the context menu for the **Start** menu, select **Shut down or sign out**, and then select **Sign out**.

1. sign in to **SEA-SVR2** by using the following credentials:
   - Username: **CONTOSO\\Administrator**
   - Password: **Pa55w.rd**

1. Start Microsoft Edge, and then browse to the Azure portal.

1. Sign in to the Azure portal by using the credentials of the account you created in exercise 1.

1. In the Azure portal, in the **Search resources, services, and docs** text box, enter **Azure Active Directory**, and then select **Azure Active Directory** from the drop-down menu.

1. On the **Azure Active Directory** page, select **Password reset**.

1. On the **Password reset** page, observe how you can select the scope of users to which to apply the configuration.

>**Note:** Don't enable the password reset feature because it will break the configuration steps that are required later in this lab.

### Task 2: Enable password writeback in Azure AD Connect

1. On **SEA-SVR2**, open **Azure AD Connect**.

1. In the **Microsoft Azure Active Directory Connect** window, select **Configure**.

1. On the **Additional tasks** page, select **Customize synchronization options**, and then select **Next**.

1. On the **Connect to Azure AD** page, enter the username and password of the user account you created in exercise 1, and then select **Next**.

1. On the **Connect your directories** page, select **Next**.

1. On the **Domain and OU filtering** page, select **Next**.

1. On the **Optional features** page, select **Password writeback**, and then select **Next**.

     >**Note:** Password writeback is required for self-service password reset. This allows passwords changed by users in Azure AD to sync to the on-premises Active Directory.

1. On the **Ready to configure** page, review the list of actions to be performed, and then select **Configure**.

1. On the **Configuration complete** page, select **Exit**.

### Task 3: Enable pass-through authentication in Azure AD Connect

1. On **SEA-SVR2**, on the **Start** menu, expand **Azure AD Connect**, and then select **Azure AD Connect**.

1. In the **Microsoft Azure Active Directory Connect** window, select **Configure**.

1. On the **Additional tasks** page, select **Change user sign-in**, then select **Next**.

1. On the **Connect to Azure AD** page, enter the username and password of the user account you created in exercise 1, and then select **Next**.

1. On the **User sign-in** page, select **Pass-through authentication**.

1. Verify that the **Enable single sign-on** check box is selected, and then select **Next**.

1. On the **Enable single sign-on** page, select **Enter credentials**.

1. In the **Forest credentials** dialog box, enter the following credentials, and then select **OK**:
   - Username: **Administrator**
   - Password: **Pa55w.rd**

1. On the **Enable single sign-on** page, verify that there's a green check mark next to **Enter credentials**, and then select **Next**.

1. On the **Ready to configure** page, review the list of actions to be performed, and then select **Configure**.

1. On the **Configuration complete** page, select **Exit**.

### Task 4: Verify pass-through authentication in Azure

1. On **SEA-SVR2**, on the **Azure Active Directory** page in the Azure portal, select **Azure AD Connect**.

1. On the **Azure AD Connect** page, review the information under **User Sign-In**.

1. Under **User Sign-In**, select **Seamless single sign-on**.

1. On the **Seamless single sign-on** page, review the on-premises domain name.

1. In Microsoft Edge, select the **Back** button to return to the previous page.

1. On the **Azure Active Directory** page in the Azure portal, select **Azure AD Connect**.

1. On the **Azure AD Connect** page, under **User Sign-In**, select **Pass-through authentication**.

1. On the **Pass-through authentication** page, review the list of servers under **Authentication Agent**.

>**Note:** To install the Azure AD Authentication Agent on multiple servers in your environment, you can open the **Azure AD Connect** page from the server, and then choose the **Download** option to install it on other servers.

### Task 5: Install and register the Azure AD Password Protection proxy service and DC agent

1. On **SEA-SVR2**, start Microsoft Edge, and then browse to the Microsoft website, and search for “Azure AD Password Protection for Windows Server Active Directory” to find the **Azure AD Password Protection for Windows Server Active Directory** page and select **Download**.

1. On the Azure AD Password Protection for Windows Server Active Directory page, select the AzureADPasswordProtectionProxySetup.exe and the AzureADPasswordProtectionDCAgentSetup.msi files, and then select Next.

1. Select **Download**.

1. In the **Download multiple files** dialog box, select **Allow**.

     >**Note:** We recommend installing the proxy service on a server that isn't a domain controller.

1. On the status bar, locate the **AzureADPasswordProtectionProxySetup.exe** file, and then select **Open file**.

1. In the **Azure AD Password Protection Proxy Bundle Setup** window, select the **I agree to the license terms and conditions** check box, and then select **Install**.

1. In the **Installation Successfully Completed** window, select **Close**.

1. Open the **AzureADPasswordProtectionDCAgentSetup.msi** file to install the DC agent.

1. In the **Azure AD Password Protection DC Agent Setup** window, select the **I agree to the license terms and conditions** check box, and then select **Install**.

1. In the **Completed the Azure AD Password Protection DC Agent Setup Wizard** window, select **Finish**.

1. In the **Azure AD Password Protection DC Agent Setup** dialog box, select **No**.

1. On the **Start** menu, select **Windows PowerShell**.

1. In Windows PowerShell, enter the following command, and then select Enter:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

1. Verify that the status is **Running**.

1. To register the proxy service with Azure AD, enter the following command, and then select Enter:

    ```powershell
    Register-AzureADPasswordProtectionProxy -AccountUpn <NewUser>
    ```

     >**Note:** Replace \<NewUser> with the fully-qualified user principal name of the account you created in exercise 1.

1. In the authentication window, enter the credentials for the account, and then select **Sign in**.

1. To register the proxy service with on-premises Active Directory, in Windows PowerShell, enter the following command, and then select Enter:

    ```powershell
    Register-AzureADPasswordProtectionForest -AccountUpn <NewUser>
    ```

     >**Note:** Replace \<NewUser> with the fully-qualified user principal name of the account you created in exercise 1.

### Task 6: Enable password protection in Azure

1. On **SEA-SVR2**, in the Azure portal, on the **Azure Active Directory** page, select **Security**.

1. On the **Security** page, select **Authentication methods**.

1. On the **Authentication methods** page, select **Password protection**.

1. On the **Password protection** page, change the slider for **Enforce custom list** to **Yes**.

1. In the **Custom banned password list** text box, enter the following words (one per line):
    - **Contoso**
    - **London**

     >**Note:** The list of banned passwords should be words that are relevant to your organization.

1. Confirm the slider for **Enable password protection on Windows Server Active Directory** is set to **Yes**.

1. Confirm the slider for **Mode** is set to **Audit**, and then select **Save**.

## Exercise 6: Cleaning up

### Task 1: Uninstall Azure AD Connect

1. On **SEA-SVR2**, on the **Start** menu, select **Control Panel**.

1. In the **Control Panel** window, under **Programs**, select **Uninstall a program**.

1. In the **Uninstall or change a program** window, select **Microsoft Azure AD Connect**, and then select **Uninstall**.

1. In the **Programs and features** dialog box, select **Yes**.

1. In the **Uninstall Azure AD Connect** window, select **Remove**.

1. After the uninstall completes, in the **Uninstall Azure AD Connect** window, select **Exit**.

### Task 2: Disable directory synchronization in Azure

1. On **SEA-SVR2**, on the **Start** menu, select **Windows PowerShell**.

1. To install the Microsoft Online module for Azure AD, in Windows PowerShell, enter the following command, and then select Enter:

    ```powershell
    Install-Module -Name MSOnline
    ```

1. When prompted to install the NuGet provider, enter **Y**, and then select Enter.

1. When prompted to install the modules from an untrusted repository, enter **Y**, and then select Enter.

1. In Windows PowerShell, enter the following command to provide credentials to Azure, and then select Enter:

    ```powershell
    $msolcred=Get-Credential
    ```

1. In the **Windows PowerShell credential request** dialog box, enter the credentials of the user account you created in exercise 1, and then select **OK**.

1. In Windows PowerShell, enter the following to connect to Azure, and then select Enter:

    ```powershell
    Connect-MsolService -Credential $msolcred
    ```

1. In Windows PowerShell, enter the following to disable directory synchronization in Azure, and then select Enter:

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false
    ```

1. When prompted to confirm, enter **Y**, and then select Enter.
