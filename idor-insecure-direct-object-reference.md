---
description: >-
  This is an access control issue when we can request a resource with an object
  ID and the web app returns the information about that object ID.
---

# IDOR - Insecure Direct Object Reference

## Method Types:

1. If you have a webpage that ends in a number and displays specific information, change the number to try to access more potentially important information. This is essentially "navigating" through potentially important webpages.&#x20;
2. FUZZ the number part and filter your FUZZ, using -fs like usual, to remove the size numbers of the webpages that don't output any useful information. Use a wordlist containing numbers.&#x20;

