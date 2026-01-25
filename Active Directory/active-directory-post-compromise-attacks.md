---
description: >-
  Insight into further attacks that can be performed on AD after compromise +
  further enumeration once we obtain an account/access.
---

# 😃 Active Directory Post-Compromise Attacks

## Pass the Password & Pass the Hash:

* This is useful if we can crack a password and/or dump the SAM hashes for lateral movement in networks.&#x20;
* Pass the Password attempts to use a user's account and password across a network to try and log in to anywhere else successfully.&#x20;



Method:

1. For a Pass the Password attack, run crackmapexec like this:

{% code title="Attempting Pass the Password across a subnet using compromised user credentials. " %}
```bash
root@kali:~# crackmapexec smb [network]/[CIDR Notation for Subnet] -u [Username] -d [Domain] -p [Password]
```
{% endcode %}

-> We use smb here because smb is a Windows protocol for remote authentication, which is exactly what we're doing here with Pass the Password.&#x20;

> If you get "Pwn3d!" for a machine, it means you have admin-level rights with those account credentials on that machine.

> A green icon means authentication is successful, but without an accompanying "Pwn3d!" message, you don't have admin-level rights.

