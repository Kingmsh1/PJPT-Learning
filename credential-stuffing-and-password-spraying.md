---
description: >-
  Credential Stuffing is basically trying breached account credentials in the
  hope of being able to successfully log in.
---

# Credential Stuffing and Password Spraying

## Credential Stuffing (with Burp):

Prerequisites:

* Make sure you have a proxy enabled. I used 127.0.0.1 at port 8080 and configured Burp to have the same.
* Make sure the BurpSuite CA certificate is installed or this won't work. Your browser has to trust the software.

***

1. On Burp, turn intercept on and try to login to a website using some credentials. I did this against tesla.com using "test@test.com" as the username and "test" as the password.
2. The intercepted traffic will show up on Burp. Right click the interface and select "send to intruder".
3. Go to the "Intruder" tab in Burp. Then, go to the "Positions" tab and select clear positions (to clear the highlights).
4. In the intercept, highlight the username and password parts that you entered and add them as positions 1 and 2 respectively. 1 means username, 2 means password.
5. &#x20;Still under "Intruder", go to "Payloads". Add your list of usernames to try under Payload Set 1. Then, add your list of passwords to try under Payload Set 2. Make sure you use the "Pitchfork" attack which allows multiple parameters (your username AND password). How this works is that it tries the first username in Set 1 with the first password in Set 2. Then, the second username in 1 with the second password in 2 etc.
6. Because you set the username and password sections as positions in step 4, Burp will substitute these with the credentials you want to stuff.
7. Start the attack.
8. Go to the attack interface. Generally, indicators of a successful login would be a significant increase in the "Page Length" size column from the usual size of the other credentials. Click on the options and skim through for indicators of "unsuccessful/successful login".

***

## Password Spraying (with Burp):

Exact same set-up as above except:

1. In the "Positions" tab, set the password to a fixed string. Don't make it a position. Keep the username as a position.
2. Switch your attack type to "Sniper". You no longer need 2 parameters. The only thing you're changing is the username.
3. Start the attack with your desired list of usernames. It will try the password for every username. Again, look out for similar indicators of a successful login.
