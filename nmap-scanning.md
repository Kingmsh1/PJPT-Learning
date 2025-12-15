---
description: Learning to scan with Nmap (Network mapper)
---

# Nmap Scanning

## Pre-requisites:

* You're going to need to find your IP address and the target IP address first.
* ARP (Address Resolution Protocol) maps IP addresses to MAC addresses so that devices can communicate on a local network.
* In Kali, run "arp-scan -l" which will give a list of all the devices on your network. You should find an IP address linked to a VM appear. This shows the mappings of IPs in a network to their MAC addresses.
* You can also run "netdiscover -r \[subnet of your network]". Example: "sudo netdiscover -r 10.0.2.0/24". This would scan the entire /24 subnet (finds alive devices with IP 10.0.2.X).
* Also, you can run "ifconfig" to get your own IP address.

***

## Nmap Background Info:

* Nmap scans for open ports and services for us using something similar to a three-way handshake (SYN, SYN-ACK, ACK).

***

## Important Nmap "Switches":

* Switches in Nmap are basically just options on how you'd like to run your Nmap command.
* The order you set your switches when writing the command in shell doesn't matter.

Examples:

```bash
root@kali:~# nmap -T4 -p- -A 10.0.2.4
```

```bash
root@kali:~# nmap -T5 -p- 80,21 -A 10.0.2.4
```



-> "-T": time. -T1 is the slowest, most thorough and accurate Nmap scan. -T5 is the fastest, most-likely-to-miss-something Nmap scan.

-> "-p-": scan all ports. Without this switch, it scans the 1000 most common ports (e.g., 443, 21, 80 etc.). There are 65535 ports altogether which could have some service we would miss. Modify by adding a specific chain of ports after the switch to scan **specific** ports. Separate the ports using commas as shown above.

-> "-A": everything. Scan all of it and give as much info as possible (e.g., OS version etc.).

-> "10.0.2.4": target. This is just an example of a target IP address on the network we are running the Nmap scan against.&#x20;



Other Switches:



-> "-sS": stealth scan. Although not considered stealthy anymore - modern security easily picks it up. Nmap modifies the three-way handshake to "SYN, SYNACK, RST". RST means RESET and it's basically Nmap cutting off the connection request from the victim after requesting it. It does this to just identify the open ports and not actually do anything further&#x20;

-> "-sU": UDP scan. Connection-less protocol scan.&#x20;

***

Example Terminal Output:

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Here, ports open include 443, 22, 80 etc. We're also told the OS which is good for enumeration later.
{% endhint %}
