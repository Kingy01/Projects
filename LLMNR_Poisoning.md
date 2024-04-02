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

