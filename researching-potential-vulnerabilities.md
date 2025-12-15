---
description: >-
  We've found the target information. Now we need to bridge the gap between
  information and vulnerabilities/potential exploits.
---

# Researching Potential Vulnerabilities

## Search Online:

* We need to search for exploits or vulnerabilities relating to the information we found about the machine.
* If you know the server the target is running on and the version just search on Google, for example, "\[server name] \[version] exploits".

Example:

"Samba 2.2.1a exploits" on Google

> Tool: exploit-db.com
>
> Description: Exploit Database has a bunch of exploits for specific system architecture/features of a target machine that can be downloaded and used.&#x20;

***

## Use The Shell:

* This is particularly useful if you don't have network access and/or you're confined to bash terminal space

> Tool: SearchSploit
>
> Description: looks through the whole Exploit Database for us (which are pre-downloaded)

Example:

{% code title="Searching for an exploit for Samba." %}
```bash
root㉿kali:~# searchsploit Samba 2
```
{% endcode %}

* Don't be too specific with searchsploit because it searches what you type exactly to find closest matches to the string.
* You may or may not get a list of exploits depending on if SearchSploit finds it.
* Check the details of each exploit listed to see if it matches your requirements (and more specifically the technical features of the machine you're running it against).

***

## Note-Taking:

* Do this for all the ports, protocols and all other information you have.
* Make fresh notes that map the specific features of the target machine to the exploits/vulnerabilities you researched online.
