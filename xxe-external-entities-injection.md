---
description: >-
  Some web apps use XML (Extensible Markup Language) to transfer data. Parsers
  that process the data for XML may be vulnerable to XXE injections (External
  Entities Injections).
---

# XXE - External Entities Injection

## Intro:

* An XML entity is a simple way of representing data or simple characters.&#x20;
* An external entity is a custom entity whose definition is outside the XML documents and therefore needs to be located when the XML file is passed.&#x20;
* You should test for XXE injections wherever you can pass or upload XML.&#x20;

***

## Delivering the Injection:

1. Find payloads online for remote code execution etc. at "payload all the things" on GitHub which you can put inside XML files.
2. Upload/pass these XML files.&#x20;
