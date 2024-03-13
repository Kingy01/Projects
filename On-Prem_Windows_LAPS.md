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

![image](https://github.com/Kingy01/Projects/assets/24928927/5bd949a7-4c0b-4f86-9089-eda5a1db2b86)

We now need to go to “Group Policy Object”, right-click on our newly created GPO and go to “Edit”.

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

![image](https://github.com/Kingy01/Projects/assets/24928927/31a6aa17-ce4f-4a7c-b2af-8c1593977912)

Then we need to click “Enable” then “Apply and Ok”.

![image](https://github.com/Kingy01/Projects/assets/24928927/16c107a7-9b76-42e1-9783-8e93fb45b4e0)

That will then enable password encryption.

The next step is setting up the “Name of administrator account to manage”, so we want to double click on this one.

![image](https://github.com/Kingy01/Projects/assets/24928927/447c6667-41f1-4355-b921-84802ab774fa)

This is where we setup what we want the local administrator account to be that LAPS manages on the workstations.

We can name it whatever we want, in this example, I have named my local admin account to be managed “LocalAdmin”, but you can choose whatever name you want it to be.

First, we need to enable it, then specify the name of the local admin account to manage, then apply the setting, then click “Ok”.

![image](https://github.com/Kingy01/Projects/assets/24928927/a3f61e5e-4f1a-44d2-a6f6-820b88bfda20)

Now that we have setup which local admin account we would like LAPS to manage, we now need to setup where we want the local admin account password to be backed up to, in this example, I am using an on-premise Active Directory set up, so I will be selecting the password to be backed up to Active Directory, however if I was using Azure AD, I would then select Azure Active Directory. In a hybrid environment, we would then have further options. 

So, we will double click on “Configure password backup directory”.

![image](https://github.com/Kingy01/Projects/assets/24928927/8ca5a848-ae09-47af-b1e7-85bafe5a3b5a)

Then we will have to enable it, then select which backup directory that we want to use, then click “Apply”, followed by “Ok”.

![image](https://github.com/Kingy01/Projects/assets/24928927/5a0dd400-a9ac-4cdd-8e35-6f4ae42f681e)

Again, here I have used “Active Directory” as my backup directory, because that’s what I am using.

The next thing we need to do is setup the Password Settings. In here we can setup the password complexity, password length and the password age, which is a pretty important security policy that we need to setup in LAPS, we need LAPS to be creating complex and lengthy passwords and recycling them at a good rate as well for good credential hygiene. 

We can set up “Password Settings” by double clicking on “Password Settings”

![image](https://github.com/Kingy01/Projects/assets/24928927/087d4437-bd52-47b7-9d39-aff93e67be44)

From there we need to set up our complexity, length and password age, I have set up large letters, small letters, numbers and specials, as this is the best practice, as for the password length, I chose 18, as 8 is too short and I still don’t personally feel comfortable with a length of 12 either, so I wouldn’t personally set this below at least 14 for a password length. I have left the password age at 30 days, after 30 days, LAPS will regenerate a new password for the local admin account.

![image](https://github.com/Kingy01/Projects/assets/24928927/ffe4e0a4-af59-4ddf-becd-ecb85788c455)

Now, we need to link the LAPS GPO that we just created to the OU of where our workstations will be, in my instance, I have a OU called Workstations, so I will be linking this GPO to my Workstations OU.

![image](https://github.com/Kingy01/Projects/assets/24928927/f3ac2885-a6b3-4e45-a14e-1c3032242274)

That will link the LAPS GPO to the OU.

![image](https://github.com/Kingy01/Projects/assets/24928927/6585725f-2e06-4467-a3ea-34baab45e28a)

I have manually created the local admin account on a workstation managed by my DC in this example, however another GPO can be created or a script can be used to create the local admin account across multiple workstations within the domain.

![image](https://github.com/Kingy01/Projects/assets/24928927/08799031-3778-48e7-b603-4d7d4101fe31)

Now, there are a few ways to retrieve the password for the local admin account managed by LAPS, we can get this from the computer object within Active Directory itself.

We just need to go to the properties of the computer object of the workstation within Active Directory and go to the LAPS tab.

![image](https://github.com/Kingy01/Projects/assets/24928927/a5814b6d-6d3a-438c-af5d-7e97f67dd14c)

As you can see, the LAPS password is visible within the “LAPS” tab of the computer object in AD.

Another option is we can actually use PowerShell to retrieve the password for the local admin account of any workstation of our choosing, so long as the workstation is in the OU of our applied LAPS GPO.

![image](https://github.com/Kingy01/Projects/assets/24928927/d05645ca-52f0-42e9-a5be-b4fc773b4ec1)

By using the PowerShell cmdlet Get-LapsADPassword, we can retrieve the password of the desired workstation’s local admin account, we just need to add the workstation hostname after -Identity within our PowerShell syntax.

Now to confirm that the password works and I can log into the workstation using the local admin account managed by Windows LAPS.

![image](https://github.com/Kingy01/Projects/assets/24928927/904658ef-ef06-4746-b3c5-e485391917cb)

The above confirms that I can log in as the local admin account managed by LAPS with no issues.














