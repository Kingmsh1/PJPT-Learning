---
description: An overview of Active Directory (AD) for internal penetration testing.
---

# Active Directory Intro

## Overview:



* Active Directory is a directory service developed by Microsoft to manage Windows domain networks (a computer network storing all user accounts, devices, printers, policy etc. for centralised management).
* This is the most widely known and used identity management service.&#x20;
* Can be exploited through abusing features, trusts, components etc. without ever attacking patchable exploits.&#x20;

## AD Logical Directory Components:



* AD DS (Domain Services) Schema: defines all types of objects that can be stored in the directory. Enforces rules on object creation and configuration.
* Class Objects: objects that can be created in the directory. E.g., users, computers etc.
* Attribute Objects: objects that store information relating to an object. E.g., display name etc.



* Domains: group and manage objects mentioned above. They are maintained here.&#x20;
* Organisational Units (OUs): AD containers that contain users, groups, computers and other OUs. OUs manage a collection of objects in a consistent manner, apply policies and hand out permissions to administer groups of objects.

***

## Domain Controllers:



* Domain Controllers: most important component. It's a server with a copy of the AD database (information for user services, application, ntds.dit etc.) directory store. It provides authentication & authorization services, allows admin access to manage user accounts & network resources&#x20;
* Domain controllers control domains (explained later).
* Domain controllers give us the rules and boundaries for applying to our domains.

***

## Trees & Forests:



* Trees: a domain tree is a hierarchy of domains in AD DS - basically a group of domains.
* "Parent" domains would be a normal domain like "domain.com". "Child" domains would be subdomains of the parent domain.&#x20;
* Forests: collections of multiple domain trees. Forests share a common schema, configuration partition, enable trusts between all domains in the forest (i.e., all domains trust all other domains in the forest).

***

## Trust:



* Trusts: have a mechanism for users to gain access to resources in other domains.&#x20;
* Directional Trusts: the direction of trust if from trusting domain to the trusted domain. This is a 2-domain trust (relationship between only 2 domains).
* Transitive Trusts: a trust relationship that goes further than a simple, 2-domain trust, including other trusted domains.&#x20;
* As stated above, all domains in a forest trust all other domains in the forest.
* Trust can go beyond the forest.
