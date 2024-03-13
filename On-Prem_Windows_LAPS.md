## On-Premise Windows LAPS

Windows LAPS is the newer solution to the legacy Microsoft LAPS, which contains all the features of the legacy LAPS with extra new features, such as the ability to have DSRM password backups, password encryption, password history, automatic rotation to name a few.

Windows LAPS can be used for both on-premise Active-Directory and Azure Active Directory scenarios.

Windows LAPS is available and supported on the follow platforms

* Windows 11 22H2
* Windows 11 21H1
* Windows 10 - April 2023 Update
* Windows Server 2019  - April 2023 Update
* Windows Server 2022 - April 2023 Update

To get started with setting up LAPS on an on-premise Active-Directory environment, we first need to open PowerShell and use the command “ipmo LAPS”, make sure your DC is fully patched before attempting this.

The command ipmo adds a module to the current PowerShell session, in this instance, we are adding the LAPS module.

![image](https://github.com/Kingy01/Projects/assets/24928927/da19756d-3780-4797-b047-53e242a53d2c)

After this, we can use the command “gcm -Module LAPS”, this will confirm if the LAPS module has been added into our PowerShell session, if we see the list of cmdlets like below, then the module has loaded successfully into our PowerShell session. 

![image](https://github.com/Kingy01/Projects/assets/24928927/0256cfc3-11cc-4b28-b3b1-4b5c7d0f9c9e)

The next step that is required is updating the Active Directory Schema, this can be done by issuing the following command “Update-LapsADSchema”, what this does is update the AD Schema by adding the LAPS attributes to the computer objects.

![image](https://github.com/Kingy01/Projects/assets/24928927/4df3b917-afd7-4646-9251-c3fb03dc22db)

You will be prompted for each LAPS Attribute that needs to be added, it’s best just to simply press “A” to accept all of the LAPS attributes.

Once the AD Schema gets updated, you can go to the Attribute Editor within the properties of a workstation, there should be several LAPS attributes added there.

![image](https://github.com/Kingy01/Projects/assets/24928927/fb12bc99-413c-4ec7-b973-f7f685b5b7df)

Now we need to grant the computer objects the rights to manage their own LAPS password, this is where the “Set-LapsADComputerSelfPermission” command comes into play. In this example, I have set up an OU called “Workstations”, this is the OU where all the workstations will go into.

![image](https://github.com/Kingy01/Projects/assets/24928927/d797c61c-de54-45c3-9c04-f2fa8cc12a7d)

The following has then granted permissions to the OU “Workstations”, now computer objects in that OU have the access to manage LAPS passwords.

Let’s confirm who has extended rights to LAPS AD of the OU “Workstations”. To do this, we can use the following command “Find-LapsADExtendedRights -Identity “OU=Workstations,DC=KINGYSECURITY,DC=local”.

![image](https://github.com/Kingy01/Projects/assets/24928927/9c11b2e3-2af8-45c3-9050-447e09d2c552)

Here, we can see NT Authority\System and Domain Admins have extended rights, which is good, we don’t want to give any more privileges than is required.

We now need to set up a Group Policy Object, this is to enable the LAPS features that we want to use within our LAPS setup.

We will set up a new GPO for this.

After opening “Group Policy Management” we will right-click on “Group Policy Objects” and click on “New”.

![image](https://github.com/Kingy01/Projects/assets/24928927/368d8927-0287-49aa-9fc2-2707b3b15fbb)

We can call it LAPS and then click “Ok”.



We now need to go to “Group Policy Object”, right-click and go to “Edit”.

![image](https://github.com/Kingy01/Projects/assets/24928927/d7df36e9-a0fa-44ef-b6e6-97f9f6168b22)

We now need to go to “Computer Configuration > Policies > Administrative Templates > System > LAPS.

![image](https://github.com/Kingy01/Projects/assets/24928927/496ceff6-c2e7-4b29-8594-a636e6bd63f8)

There are a few options that we can enable here, I have enabled a few in this example and have got a fairly straight forward setup here without adding way too much complexity.

I will go through the settings I have enabled with a top-down approach.

The first one is enabling password backup for DSRM accounts, Directory Services Restore Mode is a function that can be used to recover Active Direction Domain Services by booting up in safe mode to restore a working config of ADDS. This requires you to know your DSRM password.

So by enabling this feature in LAPS, the password for DSRM will be managed and backed up within Active Directory by LAPS.

To enable it, we need to double click on “Enable password backup for DSRM accounts” then click “enable” followed by “apply and then ok”.

![image](https://github.com/Kingy01/Projects/assets/24928927/c504e3d1-8e3c-47df-8cad-68e295463222)

The next thing we are going to do is enable password encryption, which is required for enabling the password backup of DSRM passwords and since this server is a domain controller, the backup of DSRM passwords will work!. 

Also enabling password encryption with LAPS, provides an extra security layer.

We need to double click on “Enable Password Encryption”.

![image](https://github.com/Kingy01/Projects/assets/24928927/9aa27aa1-9529-45a2-8d03-c05cfb76e448)

Then we need to click “Enable” then “Apply and Ok”.








![image](https://github.com/Kingy01/Projects/assets/24928927/5bd949a7-4c0b-4f86-9089-eda5a1db2b86)

