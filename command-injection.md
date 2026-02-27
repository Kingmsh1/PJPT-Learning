---
description: Attempting malicious code injections into vulnerable web apps.
---

# Command Injection

## Command Injection Basics:

* A vulnerability that can help us compromise an entire application and the host.&#x20;
* The app takes input from the user which is passed into a function that executes the code input &#x20;



Basics:

1. We can try command chaining. Basically adding on a payload to a normal input. Example for the lab 0x01:

```
[some normal text input]; whoami
```

-> The payload is using the ; sign. It adds on a command to be injected after it.&#x20;



```
;cat /etc/passwd    
```

-> You may not even need the normal text input and the payload may just work by itself.

-> If the information returned isn't well formatted, use Control-U to see the page raw.&#x20;



-> Use this for other payloads, like starting a reverse shell.&#x20;



***

## Command Injection Blind/Out-Of-Band:

* This is when the information isn't reflected back clearly to the attacker.
* Out-Of-Band means we may have to use an external tool/website to capture the information we retrieve from command injection.&#x20;
