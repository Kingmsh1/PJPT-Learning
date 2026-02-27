---
description: Exploiting authentication flaws in web through brute-force and logic issues.
---

# Attacking Authentication

## Intro:

* Authentication is who you are. Authorization is what you're allowed to do.&#x20;

***

## Brute-Forcing Authentication:

Method 1 (Burp):

1. On the website, attempt a login with any credentials with BurpSuite + proxy combo.
2. Analyse the request in Burp and send to intruder.&#x20;
3. Mark the password field of the request with a variable symbol.
4. Under "Payloads", add in a wordlist. Start the brute-force attack.&#x20;
5. We know if the password is correct based on the value under the "Length" tab - it will be different to the other failed attempts because it means the response of the webpage is different. Filter the length tab to find it.&#x20;



Method 2 (FFUF):

1. Copy the HTTP request from Burp and put it in a file. Here, it's called req.txt and replace the password section with the word "FUZZ"
2. In Kali, type in this command:

```shellscript
root@kali:~# ffuf -request req.txt -request-proto http -w [path to wordlist] -fs [size of failed passwords] 
```

-> "-request" flag: tells FFUF to use the HTTP request (which we copied from Burp and pasted in req.txt).

-> "-request-proto": specifies the protocol for FFUF to be using.

-> This command replaces the word FUZZ in the password section inside req.txt with words in the wordlist specified to try and find the password.&#x20;

3. Find the commonly-appearing number of size and modify the command above adding on the size filter. This will filter out the passwords that clearly don't work and will highlight the correct password found.&#x20;

