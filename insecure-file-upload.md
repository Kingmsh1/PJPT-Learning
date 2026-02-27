---
description: Uploading malicious files to help us exploit web apps.
---

# Insecure File Upload

* Client-side controls tend to be very usable but not good for security.
* The client may allow malicious files to be uploaded.&#x20;



Method (for the course lab/other pentest):

1. Intercept a normal request to upload a file on Burp (through a proxy, for example) to see the raw HTTP request.&#x20;
2. Send to the repeater and try to see if on the client-side, we can manipulate the file upload.
3. Run a PHP command containing a payload in the space where the uploaded file data was located in the HTTP request in the place of the file data. Write the filename instead as \[file].php (if the website is configured to execute .php files).&#x20;
4. The file will be uploaded if successful. Fuzz directories to the website to try and locate where the file was uploaded. Look for directories like "uploads" or "files" or something along those lines.&#x20;
5. If you find where it's located, add a parameter like this:

```
?cmd=[command to execute (maybe Linux if website is Linux-based)]
```

to the end of the URL.&#x20;



-> If file upload was unsuccessful, it means checks are happening on the server side.&#x20;

***

## Magic Bytes:

* Magic bytes are the first few bytes of a file telling the system what type of file it is.&#x20;



Method (for the course lab/other pentest):

1. Keep the magic bytes of normal, allowed files the same but inject a PHP command into the rest of the file bytes.&#x20;
2. Rename the file to something.php to allow it to be executed.&#x20;
3. If it throws an error saying the file type isn't allowed to be uploaded, keep more information at the start and end and delete the rest and see if that works.
4. Go to the directory it was uploaded in and again put a parameter at the end like this:

```
?cmd=whoami
```

-> This will be executed and displayed.&#x20;
