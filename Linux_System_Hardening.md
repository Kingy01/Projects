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

