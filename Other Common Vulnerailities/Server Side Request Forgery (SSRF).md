
SSRF allows us to induce a server to make request on our behalf
Can be used to target internal systems and resources which otherwise maybe inaccessible

Functionality of the App. We can check prices of the product on diffrent E-commerce websites, Page dosent reload and content of page changes so maybe just API requests 

Check the requests inside Repeater

![[ssrf1.png]]

Server making request
We can modify the URL to get sensitive data

Application is calling to \http://localhost/labs/api we can just Fuzz to find something useful

```sh
ffuf -r -c -w /usr/share/wordlists/dirb/common.txt:FUZZ -u http://localhost/labs/api/FUZZ
```

Output

```txt
[Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 1ms]
    * FUZZ: 

[Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 1ms]
    * FUZZ: .htpasswd

[Status: 403, Size: 14, Words: 2, Lines: 1, Duration: 1ms]
    * FUZZ: admin.php

[Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 384ms]
    * FUZZ: .hta

[Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 527ms]
    * FUZZ: .htaccess

[Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 1ms]
    * FUZZ: v2

```


Since we are fuzzing this we will get 403 forbidden acces, but if we have SSRF, and the server makes a request to it, we will be able to see the data

When we send this request in Repeater

![[ssrf2.png]]

We get status : success, so we have SSRF here

Similarly we can FUZZ for internal IP, and when we get hit on that we can find what services are they running by fuzzing port number


---


### Blind SSRF


We have the same kind of functionality like last lab

![[ssrf3.png]]

Same request being made

But when we do the same steps as previous

![[ssrf4.png]]

We get status error, Invalid data from third part

So there is probably some post processing going on

To check for SSRF in this case

Change the url to \https://webhookaddress

We still get Status : error

But on webhook, we can see a request was made, so SSRF is present

In Burp Suite Pro, we can use collaborator instead of webhooks