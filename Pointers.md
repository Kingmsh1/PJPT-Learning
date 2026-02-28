# These notes are meant to be a quick-glance reminder. These notes did not deserve a whole section to themselves, as a result.

Hash Cracking General Notes:

1. In Kali: you can use:
root@kali@~# hash-identifier [hash]. 

-> The tool will try to list all the types of hashes it thinks it is. You can use this to try and understand the type of hash to crack it.

2. For cracking the hash, you can use:
root@kali:~# hashcat -m [Module No. for Hash Type] [Path to Text File to Crack] [Path to Wordlist to Use] 

-> For example, with "-m 0", this would be used to crack a md5 hash.

3. Getting modules on HashCat to crack specific hashes: example:
root@kali:~# hashcat --help | grep NTLMv2

-> This spits out all modules that are relevant to cracking NTLMv2 hashes with "grep" basically meaning filter out stuff to do with NTLMv2. Replace NTLMv2 with other type of hashes as you so wish.

4. Try not to crack passwords on a VM because this runs off the CPU, not the GPU which is much faster than the CPU at cracking passwords. Do it on your actual machine, not the VM.


FFUF General Notes:

root@kali:~# ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt:FUZZ -u http://10.0.2.152/FUZZ

-> Uses FFUF (fuzz faster u fool) in a placeholder sort-of tool that uses a wordlist and replaces the "FUZZ" part in the -u (meaning target). It checks different input and reports behaviour for you to interpret for your attack.


ZIP File Cracking:

1. Use fcrackzip to crack password-protected ZIP files. 

2. When using fcrackzip, always use the "-u" flag to unzip to verify the correct password. Otherwise, fcrackzip may report false positives. 


SSH Logon:

1. SSH supports authentication via password or key. The private key (id_rsa) is stored locally and it it used by SSH to match to the public key (id_rsa.pub) stored in the server to prove identity. 

2. Here, the server would be the victim and the local machine would be the attacker's machine that has the private key. 

3. Use a private key for authentication like this: 
root@kali:~# ssh -i id_rsa [User]@[Target]. 

-> "-i" means identity file, specifying which private key to use. 

4. For the id_rsa file, run:
root@kali:~# chmod 600 id_rsa. 

-> This means the owner - you - gets read/write permissions for it and no one else, enabling its use in SSH when it restricts use of private key due to loose permissions. SSH will deem it secure enough to use after this. 

5. If id_rsa (or any other type of private key) is encrypted itself, crack it with ssh2john. First, convert to john format first to be able to crack it and then crack it:

root@kali:~# ssh2john id_rsa > id_rsa.hash 

root@kali:~# john id_rsa.hash --wordlist=[Path to Wordlist]
