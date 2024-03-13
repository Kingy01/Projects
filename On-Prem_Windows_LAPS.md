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
