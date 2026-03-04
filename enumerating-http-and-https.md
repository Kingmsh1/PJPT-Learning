---
description: >-
  Digging further into HTTP and HTTPS on Kioptrix (ports 80 and 443
  respectively)
---

# Enumerating HTTP and HTTPS Protocols

## Starting Off:

* If you have an IP with web protocol HTTP and HTTPS ports open, go search it up using HTTP and HTTPS both.
* The website you reach can tell you information on the machine.
* If the ports were just left open for no reason, it might show complacency. Start thinking. What other vulnerabilities could exist?
* You may find naming conventions for your machine. This can include names given to specific machines or domains to help you get a better picture of how they're utilising these names on their internal networks.
* Inspect the source code of the website page. Look for information disclosures, keys, passwords etc.
* Make notes for your report, if valuable.

***

## Web Vulnerability Scanning (Nikto):

> Tool: Nikto
>
> Description: Web vulnerability scanner in Kali Linux. Some websites with really good security (e.g., web application firewall is being used) might block Nikto scans so be careful.&#x20;

Example:

```bash
root@kali:~# nikto -h http://10.0.2.4
root@kali:~# nikto -h https://10.0.2.4
```

-> If one or the other of "http" or "https" doesn't work, switch them.

-> Summarises the vulnerabilities on the website/machine running the HTTP/HTTPS services

***

## Note Taking:

* As a reminder, good pentesters note down their findings. Make notes however you'd like.
* For this vulnerability assessment, copy the output and follow the instructions below.



1. Make a new directory:

```bash
root@kali:~# mkdir vulninfo
```

2. Go to the new directory:

```bash
root@kali:~# cd vulninfo
```

3. Make a new text file:

```bash
root@kali:~# gedit vulnerability.txt
```

4. Add info to file and save it

***

## Directory Busting (with Dirbuster):

> Tool: Dirbuster
>
> Description: Used for finding available directories for a website whilst enumerating HTTP and HTTPS

```bash
root@kali:~# dirbuster&
```

What the interface looks like:

<figure><img src=".gitbook/assets/image (2).png" alt="" width="563"><figcaption></figcaption></figure>

-> Enter the target URL of your machine as shown and specify the port (80 or 443).

-> Select the "Go faster" option.

-> Go to "Browse" and navigate to /user/share/dirbuster/wordlists and click on the wordlist you want to use. These wordlists contain well-known directories of a website. This tool is going to try and go to these.

-> It's also going to try and search for specific file extensions, which you can edit in the interface. Add more extensions, if you want, like this "php, txt, rar, zip" etc. separated by commas. The longer you make it, the longer the search will be.&#x20;

***

## Directory Busting (with Gobuster - preferred method):

> Tool: Gobuster
>
> Description: Used for finding available directories for a website whilst enumerating HTTP and HTTPS.
> Used from CLI and thus may be faster and more convenient.

Method:

1. Start Kali and run:

```bash
root@kali:~# gobuster dir -u [URL] -w [Path to Wordlist] -q
```

-> Finds directories and reduces noise (i.e., error messages) and results in clean, "quiet" output with the "-q" flag.
