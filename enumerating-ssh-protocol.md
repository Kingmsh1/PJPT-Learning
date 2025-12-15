---
description: Digging further into SSH (port 22)
---

# Enumerating SSH Protocol

It can be very easy to move from enumerating SSH to exploiting SSH. Enumerating is bound to only trying to get information from SSH. If credentials are attempted, this becomes exploitation.

***

## Process of Enumerating SSH:

1. &#x20;

{% code title="Attempt SSH into the target." %}
```bash
root㉿kali:~# ssh 10.0.2.4
```
{% endcode %}

* A KEX (Key-Exchange algorithm) is used in the SSH attempt. It's like a shared secret to decide between client and server which encryption algorithms to use to prevent MITM.
* If the KEX isn't agreed on by the SSH client (the SSH service) and the SSH server (the target) then it may throw an error saying "no matching key exchange method found".
* This means that your SSH client refuses to accept old, outdated, insecure KEX so the handshake fails.
* To overcome this do the below:



2.

{% code title="Forcing temporary acceptance of the KEX algorithm." %}
```
root㉿kali:~# ssh 10.0.2.4 -oKexAlgorithms=+diffie-hellman-group1-sha1
```
{% endcode %}

* The KEX algorithm that the SSH server offers can be used. In this case, we accept its offer to use "diffie-hellman-group1-sha1"



3. Attempt to connect to the machine. But before entering credentials stop.

***

## Enumeration Information:

* Now that we've done this, there's a possibility that in the shell, there is a banner displaying some information that could be of use.
* This is the only reason we have performed enumeration on SSH - NOT to try and break into the system.
* This was simply done in the hope of having some extra information displayed on the target.&#x20;
