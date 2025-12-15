---
description: >-
  Payloads are what we run as the exploits. Can be of different types. If a
  staged payload doesn't work, try a non-staged payload and vice versa.
---

# Staged & Non-Staged Payloads

## Non-Staged Payloads:

* Sends exploit shellcode all at the same time.
* Larger in size than Staged Payloads and won't always work.

Example:

```
windows/meterpreter_reverse_tcp
```

***

## Staged Payloads:

* Sends payloads in stages.
* Can be less stable.

Example:

```
windows/meterpreter/reverse_tcp    
```

-> Each of the parts between the slashes are the "stages".
