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








