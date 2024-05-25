## Linux System Hardening

![image](https://github.com/Kingy01/Projects/assets/24928927/e2884f38-d34b-4e11-a085-6059a71775d5)

## Disclaimer: This is for educational purposes only, I hold no responsibility if any of this information is used for nefarious purposes.

While Linux is a robust and very good open-source operating system, it doesn't come hardened outside of the box, we as sysadmins/security profressionals should prioritise hardening our Linux systems to increase security posture.

In this project I will be covering several hardening strategies with a defense-in-depth approach around physical security, file system partitioning and encryption, firewalls, remote access, securing user accounts, software and services, update and upgrade policies, audit and log configurations.

### Physical Security

Physical security of your IT assets are important and is actually a first step of security in terms of a defense-in-depth layer approach, physical security would be the first layer of a defense-in-depth approach to security.

It is not difficult for somebody to just remove a hard drive from a laptop/computer if they had physical access to your asset or they could just take your computer/laptop/server away with them if again they have access to the assets physically and the means to do it.
It won't matter about having a password on your account, there are ways to get past this for both Linux and Windows, but since I am keeping this project to Linux only, I will focus on Linux and not Windows here.

It's important that physical security to the office or where the asset actually is kept has been looked over, whether that be if the door access is using RFID or a physical key to open the door and lock it up, there are ways to get past RFID readers and even doors
that require a physical key, this is a big topic in itself and there are actually professionals that do physical penetration testing for a living, so I will just cover a few more common tactics here, but it will by no means be an exhaustive list of techniques that
are or can be used to breach the physical security of assets.

I will start with RFID, this is a very insecure technology, while it may be convenient and cool, it is not actually difficult to breach and requires very little technical knowledge to achieve, given there are devices out there already programmed to copy and clone
RFID cards/fobs for example the Flipper Zero is a very popular one circulating the market, it's small and very concealable and the only requirement is to be within a certain range of the RFID fob/card and the Flipper Zero can copy it. Once a copy has been made, the threat
actor will be able to access the building or room that requires the RFID fob/card that was copied, granting them physical access to the building/room.

An easy way to mitigate against this is by putting your RFID card/fob in an RFID blocker, these can come in keycard slips, RFID blocker wallets or even bags, the bags are known as Faraday bags. Another tip just for extra caution would be to observe who is around you
when you're using your fob/card, once the fob/card is outside of the RFID blocker, you're no longer protected, so it's wise to be aware of your surroundings if you're taking your RFID card/fob outside of the RFID blocker.

Doors that require a physical key, some doors will unlock if the door handle is opened from the inside, this can be easily breached with the right tool that can be put underneath the door and used to latch on to the door handle on the inside and pull it down, granting
access to the threat actor, another common one would be the door latches, if the door has been hung wrong, a threat actor might be able to just take out the door latches and take the door off the hinges completely.

Lockpicking can be done, however this requires actual skill to achieve, since the locking mechanism has pins, all of them need to be pushed up for the door to unlock, this can be timeconsuming to achieve and again requires lockpicking skills, not just any smuck 
will be able to pull this off successfully.

Some doors also have motion sensors from the inside of the door, the motion sensor will detect if there is movement, if it detects there is movement, the door will open, this can be easily bypassed, an example would be if someone used a vape and blew smoke through the cracks
of the door, the motion sensor can pick this up as movement and the door will open.

Ok, unfortunately looking after the physical security to access our IT assets is not enough, if that layer is breached, then it could be game over, so we also need to look at another layer, which is the physical security of the asset itself.

Having a very complex password on your Linux machine isn't enough if someone can access it physically, GRUB can be used, which is a bootloader which can reset the root password account.
Many BIOS and UEFI firmware allows an individual to set up a boot password and what this does is protect against unauthorized users booting up the system, it's used for personal laptops/computers, not servers, it's not wise to set this up on a server, it will cause a lot of headache.

So, to iterate on what I said above, setting up a boot password means that when the system is boot up, a password will need to be given in order for the system to boot up to the actual login screen.

We could use GRUB to add a GRUB password, this can also depend on what Linux system that we want to protect, there are several tools out there that can achieve this. I will discuss grub2-mkpasswd-pbkdf2.


What grub2-mkpasswd-pbkdf2 does is prompt you to input a password twice and then generates a hash, the hash then needs to be added to a configuration file, this configuration file can vary depending on which distro of Linux is being used, this then would protect against someone using GRUB to reset the root account password.

It's important to check the documentation on the particular Linux distro on how to configure the file successfully to achieve this.

### Filesystem Partitioning & Encryption

Encryption is important and what it does is encrypt data and makes it un-readable unless you have the decryption key.

As I said earlier, if someone has physical access, the means and the tools, what's stopping them from just taking the hard drive from the asset, if we encrypt the hard drive itself at least the data is protected, the threat actor won't be able to access it without the decryption key and what's even more important is setting up a very complex 

There are options in terms of tools that we can use to encrypt hard drives for Linux systems, most Linux distros come with LUKS (Linux Unified Key Setup).

Before moving further with this, I want to make it clear that this hardening technique is to mitigate against physical security, if for example you have entered the decryption key to decrypt the hard drive, this key will be in RAM (Random Access Memory), so for example say you left your desk to go for lunch, if someone had access they could do a memory dump on your machine and then use for example Volatility to get the decryption key, this goes for if someone has remote access to your computer as well. For example, say your system is infected with malware and the threat actor had access to your system remotely, this mitigation does not protect from this, it only protects you if someone steals the hard drive physically or tries to access your computer system physically while it isn't already booted and running with the decryption key already entered into the system.

I also want to point out that the implementation of Cryptsetup in LUKS has some security flaws.

If we encrypt a drive using LUKS, it will use different fields, I will put below a list of those fields and a description of them.

* LUKS phdr -  It is the LUKS partition header, it essentially stores information about the UID (Universally Unique Identifier), it also stores which cypher is being used, which cypher mode and the key length and checksum of the master key, it will also contain information about salts.

* KM  - LUKS uses key materials, which is what KM stands for and the LUKS format provides up to 8 key material sections, each one of these sections can contain a copy of the same master key and can be encrypted with a different password, which will allow different users to use different passwords to decrypt the same disk. So, for example say we have bob, he encrypts his password and it gets stored in KM1 and then we have a different user named kate, her password to encrypt the maset key then gets stored in KM5.

* Bulk Data - Bulk data is the data that is actually encrypted by the master key, which the master key is saved and encrypted in one of the key material sections such as KM1, KM3 etc.

Below is what the actual LUKS header will look like.

![image](https://github.com/Kingy01/Projects/assets/24928927/24bdaebe-2728-4a8e-aa8c-ee39d0229e5e)

LUKS does use existing block encryption implementations such as Cryptsetup for example.

Let's go over the actual syntax that encrypts data, below is the syntax.

enc_data = encrypt(cipher_name, cipher_mode, key, original, original_length)

Looking at the above syntax, we can see that LUKS requires a cypher and a cypher mode.

It's also important to note that the key supplied which will be the password supplied by the user will use PBKDF2.

We can also use a salt with our password if we wish using PBKDF2, this will use the salt with the hash function, along with an iteration count.

Below is an example of syntax to achieve this.

key = PBKDF2(password,salt,iteration_count, derived_key_length)

Now if we wanted to decrypt the data, below is an example of syntax to decrypt the data and get the data back in plaintext.

original = decrypt(cipher_name, cipher_mode, key, enc_data, original_length)

To use LUKS, the first thing that needs to be done is installing cryptsetup-luks on the Linux system, this can be done using the syntax apt install cryptsetup or yum install cryptsetup-luks, depending on which distro of Linux you're using.

![image](https://github.com/Kingy01/Projects/assets/24928927/73ad9f56-b332-46f8-baed-637746b3279a)




### Firewalls

Whether the system is acting as a client or server, or even both. We can setup a firewall on the system which will block any ports that don't need or require to be open/used on the system and only allow the ports open that we need to be open for certain services, such as HTTPS, DNS as a few examples, we can create granular rules to increase security posture on our Linux system, a firewall also allows us to create rules around source IP and destination IPs as well, there might be a scenario where we need to open a port for a service for example and we only want a certain Source IP to be able to communicate using that port to our Linux system. An example of a service could be a VPN as an example or SSH.

In a nutshell, let's compare a firewall to a security guard at a nightclub and let's say the security guard is only letting people inside the club if they have a valid ID that they can show the security guard and those that don't have a valid ID, will not get through and get access to go inside the nightclub, a firewall is like a security guard, it has rules that it has been given and going off of those rules, it decides which packets are allowed into the network and which packets are allowed outside of the network.

Firewalls act on a first match rule basis, it will start at the first rule and keep going down until the last rule, if there is no rule in-place to say the particular packet cannot leave or enter the network, then the firewall will let it inside the network or outside of the network, depending on what rules have been setup inbound and outbound.

There are several different firewalls, we have network based firewalls, host based firewalls, web application firewalls etc. Setting up a firewall on the Linux system itself will be a host based firewall. This is because the firewall will be setup on the host system itself, which will only protect the host system and not the network, where as a network based firewall will help protect a network of devices, however it's also important to note that firewalls are not 100% fool proof and can be fooled.

A firewall is just another layer of security we should use as a defense-in-depth approach, the more layers of security that we implement, the better our security posture will be, remember that there is no such thing as 100% security and another thing to note is complexity is also the nemesis to security, the more complex systems are and configured, the more opportunity for loopholes and vulnerabilities to be discovered.

There are several Linux firewalls out there that we could use, but we will cover a few very common ones here, such as iptables, ufw and nftables.

It's also important to note that Linux firewalls are stateful firewalls, meaning they will track ongoing connections and restrict packets based on particular fields within the IP and TCP/UDP headers.

### IPTables

Before we get started with iptables, it's important to note that rules need to be manually saved to be persistent, if the rules are configured and not manually saved, the rules will disappear after a reboot.

So, what I will do is, I will install the iptables-persistent package on my Linux machine, this will make it very convenient to save any rules after being configured in iptables.

![image](https://github.com/Kingy01/Projects/assets/24928927/b8b089fc-bf18-40f9-aeb6-6edf4661c9e8)

Then, I will go ahead with the installation of this package by pressing Y into the terminal and then enter.

Once that has been finished, we will be prompted if we want to save the current IPv4 rules.

![image](https://github.com/Kingy01/Projects/assets/24928927/d9d5ebe8-5fe2-46e8-8f58-68f50c2a8902)

Click "Yes" if you want the current IPv4 rules to be saved, unless there are rules that should be removed or you have messed up rules, I don't see why not.

After this, we will then be prompted if we want to save the current IPv6 rules, again, if you have any IPv6 rules and they are not requiring to be removed or fixed up, you can say "Yes", otherwise obviously there is a few options, re-do the rules again, fix any current rules that need fixing, general house-keeping.

![image](https://github.com/Kingy01/Projects/assets/24928927/abd5d814-e29c-4401-af7d-d674e207ac66)

Another thing to note is you will need sudo privileges to be able to use iptables, this includes configuring rules, removing rules, reading rules etc.

To list any current rules within iptables, we will use the following command in our Linux terminal.

![image](https://github.com/Kingy01/Projects/assets/24928927/86d64dd4-cdba-4788-81ed-4e143b55accb)

Just to prove that you need sudo privs, I will show what happens when we try to list the iptables rules without sudo.

![image](https://github.com/Kingy01/Projects/assets/24928927/d507863c-a225-4733-b0fd-1b3a0d17895c)

This is what iptables will look like with no firewall rules configured.

![image](https://github.com/Kingy01/Projects/assets/24928927/dcbccd6f-75e4-49a5-a7b2-9b00b94191ac)

![image](https://github.com/Kingy01/Projects/assets/24928927/d2aa6509-091a-4d19-8e14-67de54605e38)

How we configure the firewall rules will depend on which services we are using and what traffic needs to go inbound or outbound from the Linux machine, let's say for example we have a Linux machine that we are just going to use for browsing the Internet and just general day to day sort of things, in that case, we would need to configure some inbound rules, this would be, an inbound rule for HTTPS (443), HTTP (80) and since we would need DNS to resolve domain names, we would need to create a inbound DNS rule also.

If you have any applications that need to communicate with the localhost, which will use the localhost interface, we will need to set up a rule to allow this.

First off, I will show how to configure a firewall rule to allow traffic to the localhost, first we should check what the  name of the localhost interface is, we can do this by using the following command in our Linux terminal.

![image](https://github.com/Kingy01/Projects/assets/24928927/dbe055c3-2b3d-4db5-b51a-39d230c2c07b)

![image](https://github.com/Kingy01/Projects/assets/24928927/84d9e4f4-06aa-4755-9594-d0c362974aab)

Or alternatively, we can also use the following command

![image](https://github.com/Kingy01/Projects/assets/24928927/f3d13bb5-cd23-4d1c-88ee-667eff979ec6)

![image](https://github.com/Kingy01/Projects/assets/24928927/7376628d-01be-4d91-9dea-adc61bf50fd2)

Ok, now that we know the name of our loopback interface is called lo, we can set up a firewall rule in iptables to accept traffic to our loopback interface.

![image](https://github.com/Kingy01/Projects/assets/24928927/fcc0cb90-2a2f-4e25-8001-66eb11ad1c34)

This command will create an inbound firewall rule to accept traffic to the loopback interface.

Before we get into any other firewall rules, I will go over the switches in the syntax and what they mean.

-A means append and we want to append the rule to a chain, there are two chains, inbound and outbound. However iptables does not reference them as inbound and outbound, inbound is referenced as "INPUT" and outbound is referenced as "OUTPUT".

So when we want to create a new rule, we will use the switch -A for append and then we will use either INPUT or OUTPUT, depending on which direction we will be using for the newly created rule.

-i means for interface, when we use the -i switch we are then indicating what interface we are wanting to use within the firewall rule we are creating.

-J is for jump, which performs the action, so this is where we want to declare if the rule is to accept the traffic, drop the traffic etc.

Next, I will set up a firewall rule to drop all traffic inbound (INPUT), just to show that after I set this firewall rule up, I won't be able to browse the internet or anything successfully.

To configure this firewall rule to drop all inbound traffic, I will use the following syntax in my Linux terminal.

![image](https://github.com/Kingy01/Projects/assets/24928927/a9e826d4-7845-4825-a9b0-d40bffdcca70)

Now, let's take a look at the current iptables rules set up under the INPUT chain.

![image](https://github.com/Kingy01/Projects/assets/24928927/a5983ade-8c70-433a-840f-182dba19da76)

We can see the first rule that was set up to allow traffic to the loopback interface, followed by the deny all inbound traffic rule I created.

I then opened up my browser and tried to go to google.com and I wasn't successful.

![image](https://github.com/Kingy01/Projects/assets/24928927/2dbeea91-7aef-464b-b56a-397d89f1f6d5)

This is proof that the deny inbound traffic rule is working correctly.

At this stage you might be wondering, why are you showing me how to block all traffic on my Linux system for, I won't be able to browse the Internet or do anything enjoyable if all the traffic coming inbound to my machine is blocked, hold your horses, we will be adding rules to allow certain traffic inbound, but the reasoning for the deny rule is to show a few things, for one, show that we need inbound rules configured here in the "INPUT" chain for our Linux machine to be able to successfully access services such as HTTPS, HTTP etc successfully, we can do that by creating a rule to allow all inbound traffic, though this is severely not recommended as it's bad security practices, or we need to create specific rules to allow specific traffic to our Linux machine. 

Creating specific rules for specific traffic is the recommended path, not to just create a rule to accept everything.

Another thing it shows is, firewalls operate on a first match basis, when a firewall is going through rules, it will go through the rules from top to bottom, looking to see if a rule exists for the particular traffic and scenario it's dealing with, so basically as an example, if we have a bunch of firewall rules created and the drop all traffic rule is at the top, the firewall will continue to drop traffic despite the other rules being created.

![image](https://github.com/Kingy01/Projects/assets/24928927/4b79f7de-dcad-4d7a-a114-9685a76c2dc1)

Going back at our current firewall rules in iptables, the drop rule is at the top, so all traffic will be dropped, despite having a rule below it accepting traffic on our loopback interface even.

Basically, the lesson here is, we should have a drop all rule created, but that rule should be at the bottom of the firewall rule list, we want our accept rules to be above it and then we also want a drop all rule in our firewall rules for security, since this will then drop any other traffic that isn't specified as a rule to allow within our firewall rules.

However, iptables still seems to put the deny all rule at the top of the rules, even after creating new rules to accept traffic.

Now, I will create a rule to allow HTTPS traffic inbound, the following syntax can be used to create this rule in the iptables firewall.

![image](https://github.com/Kingy01/Projects/assets/24928927/e9c0588f-4fd7-4d3e-8280-10895ef95736)

Let's go over some of the new switches I have used here.

The -p switch is to specify which protocol I want to use, since HTTPS uses TCP, I have configured the protocol in the rule to TCP.

The --dport switch is to specify the destination port within the firewall rule, HTTPS uses port 443, so I have configured the destination port to be 443.

Here is the iptables list of INPUT rules after I created the rule to allow HTTPS traffic inbound.

![image](https://github.com/Kingy01/Projects/assets/24928927/b7c875c0-ec20-438b-b19c-12693ab0ad4d)

As you can see, as I stated earlier, the drop rule appears to be at the top of the list still. Therefore all traffic will still be dropped, let's now go over deleting rules from iptables.

I will now delete the drop all inbound traffic rule from iptables on my Linux machine, to do this, I will use the following syntax.

![image](https://github.com/Kingy01/Projects/assets/24928927/40072303-4523-477e-af45-8330be253c73)

Since I know the rule is the first rule on the firewall list, I have selected the number 1.

-D is the switch used for deleting rules in iptables firewall list.

Then you have to specify whether it's an INPUT rule or OUTPUT rule that you want to delete, in this case it was an INPUT rule, then the number is from line numbers, again since I know the rule is the first rule, the first line, I specified the number 1.

![image](https://github.com/Kingy01/Projects/assets/24928927/7e967759-5201-4f28-9908-d4f2f75b06b7)

Ok, this confirms that the deny all traffic rule has been deleted from iptables.

![image](https://github.com/Kingy01/Projects/assets/24928927/354d7843-e09d-4e24-9b6f-f22ad1c6588e)

Now I can successfully browse to Google in my browser.

Ok, now let's create a firewall rule to allow HTTP traffic inbound. We can do this using the following syntax.

![image](https://github.com/Kingy01/Projects/assets/24928927/f9376b81-622f-4076-88ee-0ad4d1f1aaf4)

It's identical to the syntax we used earlier to allow HTTPS traffic, the only difference is the destination port, in this rule we have specified port 80 as the destination port instead of port 443. HTTP uses port 80 and also uses the TCP protocol.

Now let's check the iptables INPUT chain to be sure the rule has been placed in the firewall rules.

![image](https://github.com/Kingy01/Projects/assets/24928927/887db147-8b51-4544-8f08-e775e6c59d00)

Now, I will set up a rule in the firewall to allow DNS traffic. To do this, I will use the following syntax.

![image](https://github.com/Kingy01/Projects/assets/24928927/f90c5edd-6fea-4f9d-bd59-a23c24e948ea)

Technically DNS can use both TCP and UDP protocols, however it's most common to use UDP, so in the rule, I have specified the protocol to be UDP and I have also specified the destination port to be 53, that's the port DNS uses.

![image](https://github.com/Kingy01/Projects/assets/24928927/5a8aa697-03de-4a7e-83fe-d2e096008278)

Ok, I have now verified the rule has been placed in the firewall rules of iptables.

Let's go over a rule that actually blocks an IP address for example, let's say there is a known malicious IP and you don't want any traffic coming in from this IP address as an example, we can configure a rule like this as an example.

![image](https://github.com/Kingy01/Projects/assets/24928927/e09506ba-e2e6-459e-9c77-e94761cd0912)

So, here we are creating the rule for inbound traffic using the INPUT syntax and then we are using the -s switch to specify the source, which will be the IP address that we want to block, then we will specify the action which is -j, and the action we want here is DROP. 

Let's go over what we need to do to save the rules and make them persistent, all we need to do is use the following syntax in our Linux terminal.

![image](https://github.com/Kingy01/Projects/assets/24928927/3e0fb042-35ca-42b4-a75c-d708d9014d20)

![image](https://github.com/Kingy01/Projects/assets/24928927/a3b9f248-87c6-4de9-84c1-09345a581df8)

It will generate rules that you have configured in your iptables after you submit this syntax.

## Nftables

Let's now go over nftables, nftables does not have any rules configurd out of the box, with nftables, we need to first create tables then add chains to those tables that hook into the Linux kernal as netfilter hooks, then we need to fill those chains with rules.

We will need to execute all the nft rules using sudo.

Let's go over installing nftables first, depending on the Linux distro that you're using, installing may be different, however, I will cover the install on Debian/Ubuntu and CentOS here.

For Debian/Ubuntu distros, we will install nftables using the following syntax in the Linux terminal.

![image](https://github.com/Kingy01/Projects/assets/24928927/87ca69ec-377f-4555-a0cb-864e8e457592)

For CentOS, we will install nftables using the following syntax.

![image](https://github.com/Kingy01/Projects/assets/24928927/55912495-e6b4-4d04-a7d2-742f38d04269)

Once we have nftables installed on our Linux machine, we then need to setup tables, with nftables, tables are the highest level of the nftables hierarchy, pretty much a table corresponds to a single address family and contains chains that filter packets in that address family.

Here, we will use the inet address family, simply because it is used for both IPv4 and IPv6.

But, now we need a table to add as part of the inet family, here I will add a table called "Internet"

![image](https://github.com/Kingy01/Projects/assets/24928927/e94c19d2-28be-44aa-9afa-7d0976d2ac1c)

Ok, now that I have added a table, let's now list the current nftables tables to confirm that the table has been added.

![image](https://github.com/Kingy01/Projects/assets/24928927/23795bf4-8266-4bea-b9e3-4d027b076d98)

Ok, the table has been added to the tables of nftables successfully, now it's required to create a chain for the Internet table, so I will now create a chain for the Internet table and call it Inbound, this is a regular chain that I am creating and not a base chain, just to be clear.

![image](https://github.com/Kingy01/Projects/assets/24928927/0accde81-d7d2-4316-a679-1a6f54871cb5)

What I have done here is instruct nftables to add a chain, I have then given the address family which is inet, then I have provided the table name, in my case "Internet", followed by the chain name that I want created, which is Inbound.

Now, it's always a good idea to check your work and confirm that everything is in place as it should be.

![image](https://github.com/Kingy01/Projects/assets/24928927/b7fdf03d-e3a3-4fc7-a082-3406001aea4b)

![image](https://github.com/Kingy01/Projects/assets/24928927/cd97a70d-05c2-4e8a-bf1c-832c56321699)

Ok, I have now confirmed that the chain Inbound exists within my Internet table.

Good times, now it's time to actually create some firewall rules within the Inbound chain.

However, just before I move on to doing this, I will go over a few things first, such as how to delete a chain and how to delete a table.

To delete a chain, we will use the following syntax.

![image](https://github.com/Kingy01/Projects/assets/24928927/bfc282ab-4985-44f1-b0ff-d6e6b85382df)

Now I will list the current tables within my nftables config to confirm that the chain is now removed.

I will do this by using the following syntax.

![image](https://github.com/Kingy01/Projects/assets/24928927/ed3faeda-96bf-4497-bbdf-87190456aced)

![image](https://github.com/Kingy01/Projects/assets/24928927/ed6b6f0b-717b-4d22-80bf-8d6413ed464a)

The chain no longer exists within the Internet table, now I will go over deleting a table within nftables.

We can do this by using the following syntax.

![image](https://github.com/Kingy01/Projects/assets/24928927/fb29e657-faff-4e3a-92d1-bbe05b71fc4b)

Now, to confirm that the table has actually been deleted.

![image](https://github.com/Kingy01/Projects/assets/24928927/9fabd2b3-7e1c-435b-b3ef-2385b5cee4c9)

Now, let's add some basic firewall rules to our chain within nftables.

I will start by adding a rule to allow incoming HTTPS traffic on port 443.

![image](https://github.com/Kingy01/Projects/assets/24928927/bb91e3da-192a-494a-8ed6-678cefa95d84)

Then, I will check the ruleset to confirm that the rule has been added in the Inbound chain.

![image](https://github.com/Kingy01/Projects/assets/24928927/0e13069a-5131-4c52-a745-f9e4786eb1c2)

Here, I have confirmed there is a rule for tcp 443 added within the inbound chain.

![image](https://github.com/Kingy01/Projects/assets/24928927/c434c436-4c26-40e8-8f0b-b8311b668c26)

Next, I will add a few more rules to allow traffic on HTTP and DNS as a few other examples.

![image](https://github.com/Kingy01/Projects/assets/24928927/5faa9320-97b2-49a5-9031-8ec9ea4c4901)

![image](https://github.com/Kingy01/Projects/assets/24928927/f001e103-6d08-49ab-a6a2-08c44ae0ced9)

Now I will check the ruleset and confirm that these rules have been added.

![image](https://github.com/Kingy01/Projects/assets/24928927/85ce6e7f-c5cb-42f9-a51e-aa1e7679f79e)

![image](https://github.com/Kingy01/Projects/assets/24928927/fbd0e6c5-1b6f-467f-958c-65dacfdc4ad0)

Now, if we want to save the work we have done within nftables, we can use the following syntax within our terminal.

![image](https://github.com/Kingy01/Projects/assets/24928927/3b9d218b-0786-48db-b0de-3e2e4ba5ad82)

This will save the tables, chains, rules etc you have created in nftables, so when you reboot the Linux machine, nftables will still have these persistent.

I will now go over deleting firewall rules using nftables.

First, I will obtain the handle number of the rule I want to delete within my chain, to do this, I can use the following syntax.

![image](https://github.com/Kingy01/Projects/assets/24928927/c191c9f0-9077-4486-9f70-c10c3f7d2629)

Here, I have listed the ruleset, except I have added the -a switch, what this does is list the handle numbers of the rules.

![image](https://github.com/Kingy01/Projects/assets/24928927/6d52c616-900f-4a95-a82e-b7763cbab082)

Now that I have the handle numbers of the rules, I will demonstrate deleting the rule to allow DNS traffic.

![image](https://github.com/Kingy01/Projects/assets/24928927/f924dc07-ea62-4eae-af59-b8c7fd5ba9e6)

Now to confirm that the rule has been deleted.

![image](https://github.com/Kingy01/Projects/assets/24928927/9b8cf768-08f7-4ec2-a10a-343fa2b41066)

![image](https://github.com/Kingy01/Projects/assets/24928927/987d8fa7-e432-4680-ac5e-58d9fcc58247)

This confirms that the rule has been deleted.

Out of these three firewall solutions I am showing, nftables is definitely the one that requires the most learning curve and ufw is the easiest to actually understand and implement out of all three of these firewall solutions. This is by no means an exhaustive guide, but just a guide on the basics of these three firewall solutions, but with the foundations, it is what is required to be able to configure more advanced rules within these firewall solutions and if there is any specific rule that you might need to add, the information on how to achieve this will be at your fingertips, it will just require searching online.

### UFW 

UFW is short for uncomplicated firewall and it really is uncomplicated, it's definitely the easiest firewall solution out of the three that I am showing here, the syntax is very smooth and easy to understand and it's very quick to get up and going and set up some rules and have a functional firewall within minutes.

It's important to also understand firstly that UFW is available on the Debian and Ubuntu distributions, which is also why I am showing this as an option here, simply because Debian and Ubuntu are very popular distributions, apart from also CentOS. But CentOS comes with a firewall out of the box, which is firewalld.

First I will go over how to install ufw on your Linux machine.

![image](https://github.com/Kingy01/Projects/assets/24928927/5bce725e-a56a-46fb-a88b-be5b8a0daea7)

The above is the syntax that we need to use to install ufw on our Linux system, now ufw does not come active/enabled by default, so what we can do is actually configure rules first and then after we have configured them, we can enable the ufw firewall.

There is an option to setup default policies within ufw.

If you set up the default policies, what this does is deny all incoming traffic and allow all outgoing connections.

If you're in a situation where this scenario is required, below is the syntax to set this up.

![image](https://github.com/Kingy01/Projects/assets/24928927/5f8dba04-e4b3-4d71-8ccc-e4f69052fa7f)

![image](https://github.com/Kingy01/Projects/assets/24928927/f11a5726-f6d9-4cd3-a3ff-a5ce99f68e6e)

Now, let's say that we need to set up some specific firewall rules just to allow traffic to specific services, we can configure rules to do this.

Here, I will configure a specific rule just to allow HTTPS traffic on port 443.

![image](https://github.com/Kingy01/Projects/assets/24928927/4d1844a5-ab70-4e4d-b00b-5fc9280044ff)

We can also list the protocol instead of the port, either one works, here I have stated the HTTPS port, which is 443, if I had of used the protocol HTTPS instead of the port 443, the rule would still work.





















