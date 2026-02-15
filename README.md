---
description: >-
  How to enumerate web applications further. Tools that utilise the Go language
  + automation.
---

# Web Enumeration Continued

## Installing Go:

* Install pimpmykali.sh from GitHub on Kali Linux&#x20;
* Within pimpmykali.sh, installing GoLang.&#x20;
* This will be used for the tools in this section.

***

## Finding Subdomains with AssetFinder:

* Follow the instructions on GitHub to install.&#x20;

Method:

1. Run AssetFinder against a website like this to get a list of subdomains and other assets related to the website (which may not be subdomains):

```shellscript
root@kali:~# assetfinder [website].com >> file.txt
```

-> The ">>" stores the results in a text file called file.txt in the WD.



2. From here, you can run:

```shellscript
root@kali:~# cat file.txt | wc -l
```

-> This will return the number of subdomains stored in the text file, although some domains may be duplicates.&#x20;



3. To get **only** subdomains related to the website, run this command:

```shellscript
root@kali:~# assetfinder --subs-only website.com
```



4. You can choose to create a .sh shell script to automate the process of grepping certain subdomains related only to the website we run it against like this (save it in the WD if you'd like):



```bash
#!/bin/bash

url=$1 

if [ ! -d "$url" ];then
    mkdir $url
fi

if [ ! -d "$url/recon" ];then
    mkdir $url/recon
fi

echo "[+] Harvesting subdomains with assetfinder..."
assetfinder $url >> $url/recon/assets.txt

cat $url/recon/assets.txt | grep $1 >> $url/recon/final.txt
rm $url/recon/assets.txt
```



5. Make the script executable by running:

```shellscript
root@kali:~# chmod +x script.sh
```



6. Run the script:

```shellscript
root@kali:~# ./script.sh [website].com
```

-> Here, we defined the website to be our argument in the script so you write it after ./script.sh



7. Navigate to the created text file to find all subdomains related to the website only (no other unwanted assets).

***

## Finding Subdomains with Amass (tool made by OWASP):

* Install on Kali with instructions on GitHub (again, with Go).



Method:

1. Run Amass against a specific domain like this:

```shellscript
root@kali:~# amass enum -d [website].com
```

-> "-d": domain we're running Amass against.



2. Modify our script from before to add in an Amass scan if you want to:

```bash
#!/bin/bash

url=$1 

if [ ! -d "$url" ];then
    mkdir $url
fi

if [ ! -d "$url/recon" ];then
    mkdir $url/recon
fi

echo "[+] Harvesting subdomains with assetfinder..."
assetfinder $url >> $url/recon/assets.txt

cat $url/recon/assets.txt | grep $1 >> $url/recon/final.txt
rm $url/recon/assets.txt

echo "[+] Harvesting subdomains with Amass..."
amass enum -d $url >> $url/recon/temp.txt
sort -u $url/recon/temp.txt >> $url/recon/final.txt
rm $url/recon/temp.txt
```

***

## Finding Alive Domains with HTTProbe:

* Download and install from GitHub again on Kali.
* Checks to see if the host/website is alive.&#x20;

Method:

1. Go through the file created with the script above containing all the subdomains with HTTProbe to see if the host is alive:

```shellscript
root@kali:~# cat [website].com/recon/final.txt | httprobe
```

-> If you need to, replace the file path after cat.

-> It will return a list of subdomains that are alive.

-> "|": the pipe. It takes the first half (the command before it) and uses it line-by-line as input to the command in the second half (the httprobe) and executes it.



2. Only scan HTTPS port (443) like this:

```shellscript
root@kali:~# cat [website].com/recon/final.txt | httprobe -s -p https:443
```

-> "-s": forces HTTPS

-> "-p": tells httpprobe to probe port 443 with https

-> We only scan HTTPS because most subdomains only respond to HTTPS so we get a more accurate, usable list of subdomains.&#x20;



-> This will list subdomains with "https://" at the front and ":443" at the end. Remove this (you could use AI) to get a list with just the subdomains. Find a Linux command online to do this as well.&#x20;



3. Modify the above script again:

```bash
#!/bin/bash

url=$1 

if [ ! -d "$url" ];then
    mkdir $url
fi

if [ ! -d "$url/recon" ];then
    mkdir $url/recon
fi

echo "[+] Harvesting subdomains with assetfinder..."
assetfinder $url >> $url/recon/assets.txt

cat $url/recon/assets.txt | grep $1 >> $url/recon/final.txt
rm $url/recon/assets.txt

echo "[+] Harvesting subdomains with Amass..."
amass enum -d $url >> $url/recon/temp.txt
sort -u $url/recon/temp.txt >> $url/recon/final.txt
rm $url/recon/temp.txt

echo "[+] Probing for alive domains with HTTProbe..."
cat $url/recon/final.txt | sort -u |httprobe -s -p https:443 | [Linux command to remove the https:// part and 443 part] >> $url/recon/alive.txt
```

-> Will return a list of alive HTTPS domains&#x20;

***

## Screenshotting Websites with GoWitness:

* Takes screenshots of websites.
* Install GoWitness from GitHub on Kali.&#x20;



Method:

1. Take screenshots of websites at that moment in time (i.e., what the website/subdomain would look like to view later/offline etc.):

```shellscript
root@kali:~# gotwitness single [website].com
```

-> "single": takes a single screenshot.

-> There will be other options out there for different types of screenshots.&#x20;

***

## Automating the Web Enum Process:

Some other suggestions for things to include in your Enum script could be:

* Running Nmap against subdomain list to find any open ports.
* Checking for subdomain takeover (when a subdomain on a website is down, you may be able to obtain it and have control over that subdomain on their website as a possible attack vector).



* Automation can make this process a lot easier -> it's a useful skill to learn in Linux.&#x20;
