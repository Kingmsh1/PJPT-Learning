---
description: >-
  This part of an Active Directory attack can be done once a user account is
  obtained for further enumeration on the client for further attack.
---

# Active Directory Post-Compromise Enumeration

## LDAP Domain Dump:



1. Create a folder to store the data that will be obtained from this. Make sure you have navigated to the folder.&#x20;
2. Run LDAP Domain Dump (as Administrator, within the directory):

{% code title="Using LDAP with the username and password of the account we have. " %}
```shellscript
root@kali:~# ldapdomaindump ldaps://[DC Controller IP] -u '[Domain]\[Username]' -p [Password]
```
{% endcode %}

3. You can see the information on the domain admins, expired/non-expired accounts, account creation times etc. Look at the files on domain users, policies and computers in particular.&#x20;

***

## Bloodhound:

1. Start the Neo4J console, which is required to run Bloodhound:

{% code title="Starting Neo4J console." %}
```shellscript
root@kali:~# neo4j console
```
{% endcode %}

2. Go to the remote interface link and login.
3. Run Bloodhound:

```shellscript
root@kali:~# bloodhound
```

4. Sign into Bloodhound using your Neo4J credentials.
