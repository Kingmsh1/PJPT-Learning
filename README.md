---
description: After Web Enum, we are exploiting vulnerabilities we can find.
---

# Exploiting Web Vulnerabilities

## Prerequisites:

* Install docker.io in Kali for Lab Setup.
* Install docker-compose in Kali for Lab Setup.&#x20;
* Navigate to the folder containing docker-compose.yml and run this command for setup:

```shellscript
root@kali:~# sudo docker-compose up -d

# "-d" will run this in the background so that you can free up your console 
```

* Run the ./set-permissisions.sh file to be able to run and save the files on the web server we are running.&#x20;
* Access the labs by going to "localhost" in a web browser.&#x20;
* Make sure FoxyProxy is set up at port 8080 with Proxy IP being 127.0.0.1 as HTTP proxy type (to get traffic on BurpSuite).&#x20;

***

## SQL Injection - UNION:

* We can test to see if a database is vulnerable to SQL injection by modifying the WHERE conditional clause in a SQL statement to have a search element combined with an 'or' statement combined with 1=1 (or any other equivalent true statement).
* This works because 1=1 is always true and so the SQL query will output all information where 1=1 (always true so all statements get printed).&#x20;



* We can use the "UNION" keyword in our SQL injection to get data from tables that weren't defined initially. However, with this keyword, we can only select the same number of columns as in our original query.&#x20;





***

## XSS (Cross-Site Scripting):

* This is basically when we execute JavaScript in a victim's browser to give us control over the application for that user.
* There are 3 types: Reflected, Stored, DOM-based.&#x20;
* Reflected XSS is when the script you're trying to inject comes from the current HTTP request.&#x20;
* Stored XSS is where the payload in stored somewhere, like in a database, and retrieved later. This lets you attack other users.&#x20;
* DOM-based XSS is where the client side has some vulnerable JS that uses&#x20;
