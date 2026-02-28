---
description: Extra tools used for Reconnaissance.
---

# Extra Recon Tools

## OSINT:

Hunter.io/voilanorbert.com:

Go to hunter.io/voilanorbert.com and search the name of a person/website there and it will generate emails related to them&#x20;



Dehashed.com - a store of leaked creds data:

Go to dehashed.com and input details related to a person (e.g., email) to se if you can find accounts + passwords tied to that search.

***

## Subdomain Hunting Tools (supplementary):

sublist3r:

```
root@kali:~# sublist3r -d [website]
```

-> "-d" flag means list domains related to the website.&#x20;

***

## Identifying Website Technologies:

* Can help reveal what a website is built with.



builtwith.com:

Go and look up a website. It will reveal how it runs, frameworks and what the website is written in.



whatweb:

```
root@kali:~# whatweb [website]
```

-> Returns information like country the web server is based in, the HTTP server, the IP address of the website etc.

