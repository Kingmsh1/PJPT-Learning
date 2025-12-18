---
description: >-
  NetCat is a tool used for reading/writing raw data over a network connection.
  It can make machines act as clients or servers and is used for purposes, such
  as sending files and I/O.
---

# Using NetCat

## Listening using NetCat:

* NetCat can be used on your machine for listening and writing raw data to your target machine in a "reverse-connection".

Example:

```bash
root@kali:~# nc -nvlp 1234
```

* "nc": NetCat. Launches the NetCat tool.
* "-nvlp": Use the switches -n, -v, -l, -p.
* "-n": Numeric only. It tells NetCat not to do a DNS lookup of any machine that connects to the port, which is time-consuming. It says to keep the numeric data, like IP address information, as is.
* "-v": Verbose. Give words to describe what's going on, otherwise a blank screen would just be presented and you won't know what's going on.
* "-l": Listen. Tells NetCat to listen on the port instead of connecting.
* "-p": Port number. Tells NetCat what port to be listening on for a connection.



{% hint style="info" %}
Any command you run in your local terminal running NetCat, after connection, will return output as if the command was executed on the target machine.
{% endhint %}
