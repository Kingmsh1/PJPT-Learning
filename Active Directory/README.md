---
description: Exploring further steps post-domain-compromise
---

# Post-Domain-Compromise

Post-Compromise Recommended Steps:

1. Go back and try to find different attack paths to compromise the domain. This is key so that all possible routes can be patched.
2. Dump the NTDS.dit and crack passwords.
3. Enumerate shares for sensitive information - ensures the client is storing data in the correct places.
4. Create persistence. E.g., if a DA realises their account is compromised, then it would be helpful to have a new DA account we created. Also, this would allow us to see if the client's system picks up and detects new DA accounts being created. DELETE THIS AFTER THE PENTEST IS OVER.
5. Create a Golden Ticket - could be useful.
