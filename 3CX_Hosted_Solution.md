## 3CX Free Hosted Small Business Solution

There use to be more options with 3CX, however they changed their licensing terms, not long ago, it was possible to use a free 10 user 3CX license for setting up a phone system in the cloud or on-premise, however this isn’t the case anymore and requires a licensing fee, they offer a 2 month trial period only.

First, I am going to go over the set up for the Free 10 user license hosted option, which is 3CX host it for you in one of their data centres, all you need is a SIP trunk and the 3CX application.

Another thing to note is 3CX are now trying to pull away from using an actual desktop application for 3CX, they are moving more towards PWA. Which is a progressive web app that runs as a web client and is installed from a browser such as Chrome or Edge.

First thing you will need is an account signed up with 3CX, then you will need to select the free option. 

Once we have an account set up with 3CX, we need to go to “System”

![image](https://github.com/Kingy01/Projects/assets/24928927/ecccc4ba-d830-40e0-bb7d-05305f6ad199)

That can be found in the top right hand corner by clicking on the 3 dots icon.

Then we need to click on My Systems.


Once you’re in “My Systems” click on “Add System”

![image](https://github.com/Kingy01/Projects/assets/24928927/f321c85d-0b49-4a02-a88b-033d4af567d5)

This will now give the option to setup a Free 10 user 3CX Hosted solution.

![image](https://github.com/Kingy01/Projects/assets/24928927/9756b9ac-adaa-4a5a-ba69-dac8baeb1b01)

Click “3CX Free” and then click “Next”.

This will then create your free hosted 3CX solution and then you will be brought to a screen giving details, such as username/extension, password and the URL of the webclient.

![image](https://github.com/Kingy01/Projects/assets/24928927/e0911633-fe8f-4828-be4f-f2b7099d39c4)

The next step is to use the provided webclient URL and log into it using your provided username/extension and password.

Once you’re logged into the web client, you will need to go to “Apps” and then click on “Web App PWA”.

![image](https://github.com/Kingy01/Projects/assets/24928927/0e938ff9-d697-44bd-a0cc-71d5f18ca0b4)


This will then bring up a prompt, asking to install, click “Install”.

![image](https://github.com/Kingy01/Projects/assets/24928927/950ef59f-c71f-4215-ad8b-5d13802e3f51)

After clicking install, Chrome will give you a prompt to install 3CX as a Google App.

![image](https://github.com/Kingy01/Projects/assets/24928927/9855709f-c429-4983-9be3-25994c12541c)

Click “Install”.

This will install the app within Chrome and the 3CX app will pop up.

![image](https://github.com/Kingy01/Projects/assets/24928927/e611b141-6638-407a-9f82-47d9afdee21c)

Now, we need to set up the SIP Trunk, the free hosted version is very limited in features, we only get one ring group, we cannot set up call queues or any auto attendant.

Ok, to set up a SIP trunk here, we need to go to “Admin” and then “Voice & Chat”.

![image](https://github.com/Kingy01/Projects/assets/24928927/f39cf05f-6b09-475c-9ed5-adf74285c660)

Once we are in “Voice & Chat” we need to click on “Add Trunk”

![image](https://github.com/Kingy01/Projects/assets/24928927/54660c05-ba10-4fb4-83f9-912749adc2ef)

Bare in mind, the free version only allows 1 SIP trunk to be setup.

Once you’re in the “Add Trunk” section, you will need to select your Country and Trunk, which will be from a list of providers.

It’s wise to note that, it’s best to find a SIP provider that you know works with 3CX, it’s not advisable to use a SIP provider that isn’t known to work with 3CX, otherwise you run the risk of trying to set it up and the SIP trunk doesn’t work.

In this instance, I have set up a SIP trunk with a provider from Canada.

![image](https://github.com/Kingy01/Projects/assets/24928927/c45a3883-f3d6-4adf-ab97-87d1144bde6d)

The next steps are setting up the name of the SIP Trunk and then providing the SIP username and password.

![image](https://github.com/Kingy01/Projects/assets/24928927/c0b4f433-7022-437a-ac87-8e136ec2be1a)


You can name the SIP Trunk anything, you will need to add the main business number as Main Trunk Number, but you have to add the area code in front of it, otherwise it won’t work.

Then just add the SIP User ID and password that was provided by your SIP provider, then click “Save”

![image](https://github.com/Kingy01/Projects/assets/24928927/66cc5fc3-80f6-4b72-a425-836d2d41e62c)

After adding the SIP trunk details, you will need to wait a minute or two for it to authenticate, once the SIP trunk authenticates and the credentials provided are correct, it will show a green icon next to the SIP trunk.

![image](https://github.com/Kingy01/Projects/assets/24928927/8d1f37ed-d53c-48d8-91a0-c8f415a7dc6b)

You can set up some office hours and where the calls get routed when the office is open and also where you want the calls to be routed when the office is closed. All of this can be found under “Office hours”.

![image](https://github.com/Kingy01/Projects/assets/24928927/983e86e9-3879-48fd-b23b-7121d147e166)

Make sure within “Office Hours” you assign the DID number of the main business number in here before making phone calls. Otherwise the phone calls will not come through.

![image](https://github.com/Kingy01/Projects/assets/24928927/8b201627-3312-4cff-8e08-9a8efd0c60c3)

Once the DID was assigned of the main business number, I made a test call and the call came through just fine.

![image](https://github.com/Kingy01/Projects/assets/24928927/644b4c16-1329-449c-96df-9c9b0f7bbad0)












