---
description: >-
  Digging deeper into enumerating SMB file share (port 139) between devices used
  in common work or internal environment using Metasploit - an exploitation
  framework - and SMB Client.
---

# Enumerating SMB (Server Message Block) Protocol

## Setting Up Metasploit:

{% code title="Start Metasploit in Kali Linux." %}
```bash
root@kali:~# msfconsole
```
{% endcode %}

***

## Getting Started:

{% code title="Search and list modules related to SMB in Metasploit's module database." %}
```
msf6 > search smb
```
{% endcode %}

* This is going to list a lot of modules. We want specific ones for SMB enumeration.
* We know the auxiliary module is enumeration in this case (auxiliary modules in msf help for specific parts of pentesting) so the module name in the list that we want will start with "auxiliary".
* The second part of the module name will be specific purposes. E.g., "scanning", "fuzzing" or "dos".
* We want SMB version so we're going to look for a module called "auxiliary/scanner/smb/smb\_version".

***

## Using The Module:

1.

{% code title="Use the selected module. Two alternative methods." %}
```
msf6 > use auxiliary/scanner/smb/smb_version

msf6 > use [module index number]
```
{% endcode %}

2.

{% code title="See what info is available. Tells you about the module you're running." %}
```
msf6 auxiliary(scanner/smb/smb_version) > info
```
{% endcode %}

3.

{% code title="Set the remote (i.e., target) host. " %}
```
msf6 auxiliary(scanner/smb/smb_version) > set RHOSTS 10.0.2.4
```
{% endcode %}

4.

{% code title="Run the module against the target." %}
```
msf6 auxiliary(scanner/smb/smb_version) > run
```
{% endcode %}



Example Output:

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

***

## Setting up SMB Client:

* SMB Client attempts to connect to the file share that is out there.
* If we can connect successfully anonymously then we may be able to see the files in the network, which could be extremely useful.
* If you don't know the credentials, just try to press enter or log in. If it works, it means the server allows anonymous access and will list shares without credentials - poor security.
* If credentials are required, try specifying a user.

{% code title="Lists SMB shares the target machine is offering." %}
```bash
root@kali:~# smbclient -L \\10.0.2.4
```
{% endcode %}

-> "-L": list. Lists out the files.

-> Share types listed include: "Disk" (file share with folders/files), "IPC" (Inter-process communication - not a file system), "Printer" (printer-related file share).

{% code title="Specifying a user for the SMB client." %}
```bash
root@kali:~# smbclient -L \\10.0.2.4 -U [username]
```
{% endcode %}

***

## Trying to Connect to a Fileshare with SMB Client:

{% code title="Trying to connect to the specific ADMIN$ fileshare in the target with SMB Client." %}
```
root@kali:~# smbclient \\\\10.0.2.4\\ADMIN$
```
{% endcode %}

{% code title="Trying to connect to the specific IPC$ fileshare in the target with SMB Client." %}
```
root@kali:~# smbclient \\\\10.0.2.4\\IPC$
```
{% endcode %}

* Try Linux commands, like "ls" for listing files, to see if you can extract some sort of useful information.
