---
description: Some initial attacks that can be performed on Active Directory
---

# AD Initial Attack Vectors

## LLMNR Poisoning:



* This stands for Link Local Multicast Name Resolution. It's former name was NBT-NS.
* LLMNR helps to identify hosts when the DNS fails.
* Its vulnerability comes from the fact that, when responded to correctly, it gives a user's username and a NTLMv2 hash in a MITM attack, which we may be able to crack to gain credentials.



Method (using Responder):

1. Start Kali Linux and start Responder like below:

{% code title="Starting Responder on Kali Linux" overflow="wrap" %}
```bash
root@kali:~# sudo responder -I eth0 -dwP
```
{% endcode %}

-> "-d" switch: we are answering and responding to DHCP broadcast requests.

-> "-w" switch: WPAD rogue proxy server switch. Helps us capture some hashes.

-> "-P" switch: forces authentication for the proxy



2. This will capture hashes, with information on the type of hash (NTLMv2 below), the domain, the username logged in and the password hash. Example:

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

3. Time to crack the hash. Store the hash in a text file and attempt to crack it using HashCat. Example:

{% code title="Using module 5600 (NTLMv2 hash cracking module) to crack hashfile.txt containing the has using the wordlist whose path is specified." %}
```shellscript
root@kali:~# hashcat -m 5600 hashfile.txt /usr/share/wordlist.txt        
```
{% endcode %}



LLMNR Poisoning Mitigation:

* Disable LLMNR/NBT-NS.
* If we can't disable LLMNR/NBT-NS, implement Network Access Control to prevent unauthorised devices communicating on a network and prevent them from responding to LLMNR requests and thus obtaining hashes from the victim machines.&#x20;



***

## SMB Relay Attacks:



*
