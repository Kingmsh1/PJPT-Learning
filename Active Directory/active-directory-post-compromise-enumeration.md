---
description: >-
  This part of an Active Directory attack can be done once a user account is
  obtained for further enumeration on the client for further attack.
---

# Active Directory Post-Compromise Enumeration

## LDAP Domain Dump:

This can help you to see:

* information on the domain admins&#x20;
* expired/non-expired accounts
* account creation times etc.&#x20;

{% hint style="info" %}
Look at the files on domain users, policies and computers in particular.&#x20;
{% endhint %}



Method:

1. Create a folder to store the data that will be obtained from this. Make sure you have navigated to the folder.&#x20;
2. Run LDAP Domain Dump (as Administrator, within the directory):

{% code title="Using LDAP with the username and password of the account we have. " %}
```shellscript
root@kali:~# ldapdomaindump ldaps://[DC Controller IP] -u '[Domain]\[Username]' -p [Password]
```
{% endcode %}

-> Use "ldap" instead of "ldaps" if it doesn't work - LDAPS requires port 636, not 389 like LDAP.

3. You can see the information on the domain admins, expired/non-expired accounts, account creation times etc. Look at the files on domain users, policies and computers in particular.&#x20;

***

## Bloodhound:

After compromising a user account, Bloodhound can help you identify information like:

* number of domains in the forest
* number of computers
* number of users
* number of groups etc.



Method:

1. Start the Neo4J console, which is required to run Bloodhound:

{% code title="Starting Neo4J console." %}
```shellscript
root@kali:~# neo4j console
```
{% endcode %}

2. Go to the remote interface link and login.
3. Run Bloodhound:

{% code title="Starting Bloodhound." %}
```shellscript
root@kali:~# bloodhound --no-sandbox
```
{% endcode %}

4. Sign into Bloodhound using your Neo4J credentials.
5. Make a new directory/folder. Go into it and run this command:&#x20;

{% code title="Using Bloodhound to extract information on the AD using a compromised user. " %}
```shellscript
root@kali:~# bloodhound-python -d [Domain] -u [Username] -p [Password] -ns [Domain Controller] -c all
```
{% endcode %}

-> "-ns": the nameserver. This is just the Domain Controller.

-> "-c": collect. Specifies what is being collected through this.&#x20;

6. You can view the data by looking in the directory.&#x20;
7. Upload the data into Bloodhound in the remote interface.&#x20;
8. Under 'Node Information', go to 'Analysis'. Here, you'll find information like who the domain administrators and passwords. This is particularly useful if it can help you compromise a user part of a special group, like the domain administrators.&#x20;



***

## Plumhound:

* A tool for red, and purple-teams.
* Analyses the information we got from Bloodhound before.

{% hint style="info" %}
Ensure Bloodhound (and Neo4J) is still running to be able to use Plumhound.&#x20;
{% endhint %}

Method:

1. Make sure you are in the directory with the PlumHound.py executable.
2. Analyse the information from Bloodhound through this command:

{% code title="Ensuring connection can be established with the Bloodhound database." %}
```bash
root@kali:~# python3 PlumHound.py --easy -p [Neo4J Password]
```
{% endcode %}

2. Run a set of predefined analysis tasks with this command:

{% code title="Connecting to the Bloodhound Neo4J database and running a set of predefined analysis tasks." %}
```bash
root@kali:~# python3 PlumHound.py -x tasks/default.tasks -p [Neo4J Password]
```
{% endcode %}

3. Go to the folder where the reports are stored. Open a file called 'index.html'. You'll have the analysis done by PlumHound without having to go through individual data files. Information includes OS, last login, sensitivity of accounts etc.
