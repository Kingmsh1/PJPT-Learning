---
description: >-
  Insight web exploitaiton with Cross-Site Scripting. Same labs from SQLi are
  used here.
---

# XSS

## Introduction:

* Lets us execute JavaScript in a victim's browser and often gives us control over the application for that user.&#x20;
* Reflected XSS is where the script the attacker tries to inject comes from the current HTTP request. A request is sent and a response is received. The script is included in the response. Can mostly just target the machine from which the attack is being sent from.&#x20;
* Stored XSS is where the payload is stored somewhere and retrieved later. For example stored in and retrieved from a database. This allows attacking other users.&#x20;
* DOM-Based XSS is when the client has some vulnerable JavaScript that uses untrusted input instead of having a vulnerability server side.&#x20;

***

## XSS - DOM Lab (examples of payloads):

* Try to get the browser to execute JS like this:

```javascript
<script>prompt(1)</script>        
```

-> The \<script> and \</script> parts of the code simply start tell the browser to execute JavaScript.

-> prompt(1) is a simple XSS payload that tells the browser to open a prompt dialog with the text "1". Harmless but tests if JavaScript can be executed.&#x20;

-> Won't work if \<script> is escaped or sanitised by the system, preventing JS from being executed.&#x20;



* Try to execute another payload, if this one doesn't show up and give a JS prompt:

```js
<img src=x onerror="prompt(1)">    
```

-> Tells the website to try and locate an image file from a source. If it fails, execute the command specified after "onerror".&#x20;



* You can even redirect a user using this JS prompt:

```javascript
<img src=x onerror="window.location.href='https://[website]'">
```

-> On error, it would redirect the user to the website.&#x20;



***

## XSS - Stored Lab

* Learn Stored XSS from elsewhere.
