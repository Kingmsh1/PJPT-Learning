---
description: How to enumerate DNS (port 53) on a pentest.
---

# Enumerating DNS

## Introduction:

* DNS stands for Domain Name System and is used to resolve hostnames to IPs, or the other way round.
* DNS servers manage clients' requests for resolution and store DNS records like A records (hostname→IP), PTR records (IP→hostname), MX records (mail servers), NS records (other DNS servers) etc.
* Enumerating DNS (port 53) involves querying the victim machine, assuming it's a DNS server, for information such as A, MX, NS and CNAME (aliases) records to obtain more information.
* DNS Zone transfers are when DNS servers replicate/sync their records to other DNS servers. E.g., for backup purposes from the primary DNS server to the backup DNS server.
* Misconfigured DNS servers will give this information to anyone who requests it, not just other DNS servers. This only works if it's actually a DNS server with port 53 open (and 53 isn't open for some other purpose)

***

## Method:



1. Know what domain the victim machine, who we assume is a DNS server, is serving. In AD environments, this would be the AD domain. For non-AD environments, you may have to run a reverse lookup to find the domain/zone it's serving like this:

```shellscript
root@kali:~# dig -x [Target IP] @[Target IP]
```

-> "dig": domain Information Groper. It's a Linux CLI tool to query DNS servers that hold records for every machine that's been registered in the domain.

-> "-x": reverse DNS lookup. Works by getting the IP address resolved to a hostname, which will reveal the domain being served. This can be used for zone-transfer attacks in step 2.&#x20;



2. Query the machine for information on A, MX, NS and CNAME:

```shellscript
root@kali:~# dig axfr @[DNS Server] [Domain]
```

-> "axfr": this is the query type (zone transfer). Stands for Asynchronous Full Transfer Zone. This is a request for all hostnames + IPs (A), Mail servers (MX), other DNS servers (NS), aliases (CNAME).

-> DNS server will be our victim.

-> Domain will be the domain that the DNS server is serving.&#x20;

-> This can reveal information on what machines are on the network and their information for further attack.&#x20;





