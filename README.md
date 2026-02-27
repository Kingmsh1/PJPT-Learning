---
description: >-
  Insight into post-enumeration of web vulnerabilities. Moving onto
  exploitaiton.
---

# Finding & Exploiting Common Web Vulnerabilities

## Prerequisites (Lab setup):

* On Kali, install docker.io and docker-compose modules.
* Go to the directory containing the unzipped labs and run this command:

```shellscript
root@kali:~# docker-compose up
```

-> To stop the containers, replace "up" with "stop"



* In the folder, run the ./set-permissions.sh command&#x20;
* Access the labs by going to "localhost" in the browser.
* Set up BurpSuite like this:&#x20;

1. Go to "Intercept" within "Proxy" in Burp
2. Open the browser and you will be able to capture traffic.

* If you need to reset the lab for any reason, go to "localhost/init.php"

***

## SQLi with Union:

* This lets us access information from other tables and columns that were not initially defined.
* The website presents the combined results of both SQL queries joined together with "UNION" to help us display internal database information. This is done by making a fake row using the values we provide through the second SELECT command. In the visible column, we can use this to tell SQL to return information we want in the fabricated row, like version() and SQL will just do it.&#x20;
* When we UNION select, we can only select the same number of columns (attributes) as in the original query).
* Example (for lab 0x01):



1.

```sql
jeremy' UNION SELECT null,null,null#

-- We tried first 1 null, then 2 nulls and then it worked with 3 nulls to figure out how many columns the original query had.
-- The # sign just comments the rest of the SQL query out so it is ignored.

-- The original vulnerable query might look something like this (what we are injecting into):

SELECT id, username, password FROM users WHERE username = 'jeremy';

-- This query returns 3 columns so we tested and found 3 nulls returning info successfully (one for each column).
```

-> Here, we use "jeremy'" because the original SQL query also starts off the string literal for you with an implicit "'" so you just have to close the string literal with a "'" at the end of jeremy, which prevents the rest of the SQL injection from being included in the string.

-> We trial and error the number of nulls in our SQL injection because we don't know the SQL database schema. This means we have to probe it - this is enumeration.

-> Nulls are a valid data type to use to match the data type of attributed in the table so that's why it works to put them in our SQL injection.

-> Our injections end up inside the WHERE clause because the web app places our input there before the SQL query is sent to the database.



2. We need to find the "visible" column. During this attack, the database will return a full row with all columns from our injected SELECT SQL injection. The webpage, however, will usually display only one of these columns - this is the visible column whose output actually appears on the page. Do this by testing which column reveals output:

```sql
jeremy' UNION SELECT 'A',null,null#
-- If the page shows A, then column 1 is visible. Repeat like below.

jeremy' UNION SELECT null,'B',null#
-- If the page shows B then column 2 is visible etc.
```



3. From here, we have to place our payload in the visible column. Example (assuming column 3 is visible):

```sql
jeremy' union select null,null,version()#

jeremy' union select null, null, table_name from information_schema.tables#

jeremy' union select null, null, column_name from information_schema.columns#
```

-> Figures out the version number. Other payloads can be used in the visible column.&#x20;

-> These payloads may be standard for the SQL versions. If not, find equivalents.&#x20;



4. If you find a sensitive column, like "passwords", you can run an SQL injection like this:

```
jeremy' union select null,null,password from injection0x01#
```

-> We need that first SELECT query because without it, the page would have nothing to display for the second SELECT query in the UNION.



***

## SQLi Blind:

* Remember to have the scope for Burp to http://localhost only for HTTP traffic.
* This is essentially when an SQLi executes on the vulnerable webpage but it doesn't show results.&#x20;



1. Open browser intercept from Burp and go to Injection0x02 lab. From here, log in and analyse the traffic intercepted on Burp. We need to test for injections.
2. Send the information to the Burp repeater - lets us send and modify requests to see the different responses we get back.&#x20;
3. Try basic payloads like below:

```sql
jeremy' or 1=1# 
```

-> This is for the username.

-> Make sure to URL encode the input because the SQL characters otherwise would be treated differently to how we intend for it to be executed.&#x20;

-> Look for signs of successful logon. E.g., the 'Content-Length' of the webpage might be the same number as when you logged on the first time successfully, indicating you reached the successful logon page.



4. You can automate the process of payloads using SQL Map like this:

```shellscript
root@kali:~# nano request.txt
root@kali:~# sqlmap -r request.txt

```

-> request.txt holds the text in the "Request" section under "Repeater" in BurpSuite. This is because SQL Map needs a full, raw HTTP request to understand the request and details needed to find an SQL injection.&#x20;

-> The sqlmap command tests for finding SQL injections.

-> "-r": tells SQL Map to load the exact HTTP request from the text file and use it as-is.&#x20;



5. If it fails to find SQL injections, you have the option to search manually, downloading payloads for fuzzing or trying to look for other injection points. Look at the information that is being processed. It's there for a reason and maybe you can exploit it.
6. For this lab, copy the HTTP request that has the session cookie (in /labs/i0x02.php) because we know the cookie will be processed to give the "Welcome to the dashboard" because that's what shows up in the Response. Paste the request to the Burp Repeater. Also get the response section ready.&#x20;
7. Run a SQL injection in the session cookie part by adding on:

```sql
 ' and 1=1#
```

to the end. We are successful because the Content-Length of the website stays the same, signalling we are still on the "Welcome to the dashboard" page. This signals that we can run SQL injections.&#x20;

5. This doesn't get us data yet. We have to create payloads that produce True/False output. Based on this behaviour, we can extract data. E.g., "Is the password longer than 10 characters? Yes or No." etc.&#x20;
6. We will use the SQL SUBSTR(String, num1, num2) function. This extracts a substring from String starting at position num1 and extracting num2 characters.&#x20;
7. The payload we will add to the session cookie will be:

```sql
' and substring((select version()), 1, 1) = '7'#
```

-> We are trying to see if the version of the database starts with a '7'.

-> We put select version() in brackets to let it resolve.

-> A fail indicator would be the Content-Length of the website changes from the 'Welcome to the dashboard' page Content-Length. A success indicator would be we are still on that page.

-> Trial and error until you get it. Versions of databases look like X.X.X.&#x20;



8. Another type of payload:

```sql
' and substring((select password from injection0x02 where username='jessamy'), 1, 1) = 'b'#
```

-> Trial and error to find the password string.

-> You have to automate this or it will be tediously long.&#x20;



9. Use SQL Map for the attack. Grab the original request, put it in a text file and run SQL Map against it.&#x20;

```shellscript
root@kali:~# sqlmap -r req.txt --level=2
```

-> "--level=2": tests more parameters (e.g., URL, cookies etc.), uses more payloads, increases chances of finding SQLi. Can increase scanning times a little.



-> Will return what seems to be exploitable. It will give us a payload to test for a vulnerable parameter.&#x20;



10. Alternatively, try just dumping info from the database like this:

```shellscript
root@kali:~# sqlmap -r req2.txt --level=2 --dump 
```

-> It will only dump data if SQL Map finds at least one injectable parameter in the req2.txt file supplied (only if it confirms a working SQLi).&#x20;

-> If there is no injectable parameter, it will not dump anything.&#x20;

-> Using a higher level increases the chances of finding something wrong but is more aggressive.&#x20;

-> Add on a "-T \<table name>" flag to specify a specific table to dump (if you don't want all the tables in the database) provided SQL Map finds a parameter that is injectable.&#x20;
