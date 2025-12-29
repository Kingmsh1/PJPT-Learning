---
description: An overview of Active Directory (AD) for internal penetration testing.
---

# Active Directory Intro

## Overview:

* Active Directory is a directory service developed by Microsoft to manage Windows domain networks (a computer network storing all user accounts, devices, printers, policy etc. for centralised management).
* This is the most widely known and used identity management service.&#x20;
* Can be exploited through abusing features, trusts, components etc. without ever attacking patchable exploits.&#x20;

***

## AD Physical Directory Components:

* Domain Controllers: most important component. It's a server with a copy of the AD database (information for user services, application, ntds.dit etc.) directory store. It provides authentication & authorization services, allows admin access to manage user accounts & network resources&#x20;
* Domain controllers control domains (explained later).

***

## AD Logical Directory Components:

* AD DS (Domain Services) Schema: defines all types of objects that can be stored in the directory. Enforces rules on object creation and configuration.
* Class Objects are one type of object that can be created. These are the objects that can be created in teh directory. E.g., users, computers etc.
* Attribute Objects are another type of object that can be created. These store information relating to an object. E.g., display name etc.



* Domains:&#x20;

