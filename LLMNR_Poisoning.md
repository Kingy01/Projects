## LLMNR Poisoning

### Overview

LLMNR stands for Link-Local Multicast Name Resolution, it is a protocol that allows IPv4 and IPv6 hosts to perform name resolution for hosts within the name LAN and it doesn't require a DNS server to achieve this, or any DNS configurations for that matter either.

The issue with LLMNR is that it doesn't require any authentication, this opens a door for anyone to be able to respond to an LLMNR request, therefore, it is very easy to take advantage of this and perform a man-in-the-middle attack, this can be achieved when a computer tries to resolve a domain
name and DNS is not being used to resolve the domain name, because of this, an LLMNR query will be sent across the LAN, asking other hosts on the network if it knows where to find the specific host that it tried to resolve, an attacker can basically respond to this LLMNR request saying that they know
how to reach the host, but then they ask the victim for their hash, the victim then passes the attacker their hash and this is where things spiral from here.

### Exploiting LLMNR

To achieve an LLMNR attack, the attacker must listen for LLMNR requests and when they get the requests, respond with a decoy IP or their own IP address, in turn this can lead to a few attack vectors, relay attacks and credential theft.

Below, I will go over how this is achieved.

I will be using a tool called Responder on Kali Linux, the first step is to get Responder to listen on the ethernet interface, this will start an LLMNR poisoner, along with NBT-NS, DNS, DHCP and MDNS as well.

![image](https://github.com/Kingy01/Projects/assets/24928927/87494192-a30f-4a5a-a279-e894ce7632e0)

Responder will then listen for events, respond to them and get information such as user hashes, as per my example below.

![image](https://github.com/Kingy01/Projects/assets/24928927/f4e4916f-03b7-44e3-a04a-1e802671dfa3)

What happened was the victim tried to connect to a network share that didn't exist, responder got the query and then responded with it, saying that they know how to reach that network share, we were then able to obtain the password hash of the victim.

When any LLMNR event occurs inside the LAN, responder can respond to that maliciously and obtain user hashes.

We can then use hashcat to try and crack the hash, however if the password is strong and it isn't in any dictionary lists that are used, then we won't be able to crack the hash.

## LLMNR and NBT-NS Mitigation

The good news to all of this is both of these are very easy to mitigate against, we will start off with how to mitigate LLMNR, followed by how to mitigate NBT-NS.

To disable LLMNR, we can create a group policy and push it out to all domain joined hosts on the network, to do this we will create a new group policy object, we can do this by right-clicking on "Group Policy Objects" and going to "New"

![image](https://github.com/Kingy01/Projects/assets/24928927/be39c74c-bcb6-415b-9c3a-523e08b5e0d3)

We can then give it a name, I called my policy "LLMNR", then click "Ok".

We now need to right-click on our newly created policy, in my case "LLMNR" and go to "Edit".

![image](https://github.com/Kingy01/Projects/assets/24928927/b909dd37-f1bf-4452-a4ec-1d5fd68146e4)

Now, go to Computer Configuration > Policies > Administrative Templates > Network > DNS Client

The policy within DNS Client that we want to make changes to is "Turn off multicast name resolution".

![image](https://github.com/Kingy01/Projects/assets/24928927/1e271c6c-b655-4341-bc2f-5450c486b896)

Double-click on "Turn off multicast name resolution.

![image](https://github.com/Kingy01/Projects/assets/24928927/41219e46-5542-4343-9d75-c6acab9da390)

We then want to click on "Enable", then "Apply" then click "Ok".

We then want to apply this GPO to our workstations OU, whatever OU you're keeping your workstations in, that's the OU that you want to apply the GPO to.

In my case, in my current environment here, my OU is "Workstations".

Right-click on the OU where your workstations are and go to "Link an Existing GPO".

![image](https://github.com/Kingy01/Projects/assets/24928927/db5f3206-2178-4489-9089-68c1468f2613)

Select the name of your LLMNR GPO, in my case it's "LLMNR".

![image](https://github.com/Kingy01/Projects/assets/24928927/341149f3-6b12-4c06-bbb6-9a70afff1ff1)

Then click "Ok".

Now, let's disable NBT-NS via GPO, we can create a new GPO and call it NBT-NS using the same instructions as above.

![image](https://github.com/Kingy01/Projects/assets/24928927/d451f220-88a4-447f-8e6a-386f6cacce6c)

We will write a PowerShell script and then add the PowerShell script to our NBT-NS GPO to run on startup, this will then make a registry change on all workstations that are joined to our domain and basically what the registry change will be doing is disabling NetBIOS over TCP/IP.

This is the PowerShell script that we will be using.

set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\services\NetBT\Parameters\Interfaces\tcpip* -Name NetbiosOptions -Value 2

We can open a notepad and put this in there and then save it as a .ps1 extension. 

![image](https://github.com/Kingy01/Projects/assets/24928927/3173e271-6a69-45e0-8818-420d81952f45)


![image](https://github.com/Kingy01/Projects/assets/24928927/c8448aa3-147a-4a0a-80aa-14300b830d2c)

Now, let's go back to our NBT-NS GPO and add this script to the GPO as a Startup Script.

We can do this by going to Computer Configuration > Windows Settings > Scripts > Startup 

![image](https://github.com/Kingy01/Projects/assets/24928927/2006a335-6d06-4531-8b6b-699a680851e2)

Then click on "Add".

![image](https://github.com/Kingy01/Projects/assets/24928927/6d683f9d-808f-47c4-8d4b-b437aceb61cf)





                                         ███████████████████████████
                                         ███████▀▀▀░░░░░░░▀▀▀███████
                                         ████▀░░░░░░░░░░░░░░░░░▀████
                                         ███│░░░░░░░░░░░░░░░░░░░│███
                                         ██▌│░░░░░░░░░░░░░░░░░░░│▐██
                                         ██░└┐░░░░░░░░░░░░░░░░░┌┘░██
                                         ██░░└┐░░░░░░░░░░░░░░░┌┘░░██
                                         ██░░┌┘▄▄▄▄▄░░░░░▄▄▄▄▄└┐░░██
                                         ██▌░│██████▌░░░▐██████│░▐██
                                         ███░│▐███▀▀░░▄░░▀▀███▌│░███
                                         ██▀─┘░░░░░░░▐█▌░░░░░░░└─▀██
                                         ██▄░░░▄▄▄▓░░▀█▀░░▓▄▄▄░░░▄██
                                         ████▄─┘██▌░░░░░░░▐██└─▄████
                                         █████░░▐█─┬┬┬┬┬┬┬─█▌░░█████
                                         ████▌░░░▀┬┼┼┼┼┼┼┼┬▀░░░▐████
                                         █████▄░░░└┴┴┴┴┴┴┴┘░░░▄█████
                                         ███████▄░░░░░░░░░░░▄███████
                                         ██████████▄▄▄▄▄▄▄██████████
                                         ███████████████████████████

███████████████████████▀████████████████████████████████████████████████████████
█▄─█─▄█▄─▄█▄─▀█▄─▄█─▄▄▄▄█▄─█─▄███─▄▄▄▄█▄─▄▄─█─▄▄▄─█▄─██─▄█▄─▄▄▀█▄─▄█─▄─▄─█▄─█─▄█
██─▄▀███─███─█▄▀─██─██▄─██▄─▄████▄▄▄▄─██─▄█▀█─███▀██─██─███─▄─▄██─████─████▄─▄██ 
▀▄▄▀▄▄▀▄▄▄▀▄▄▄▀▀▄▄▀▄▄▄▄▄▀▀▄▄▄▀▀▀▀▄▄▄▄▄▀▄▄▄▄▄▀▄▄▄▄▄▀▀▄▄▄▄▀▀▄▄▀▄▄▀▄▄▄▀▀▄▄▄▀▀▀▄▄▄▀▀
                                    
