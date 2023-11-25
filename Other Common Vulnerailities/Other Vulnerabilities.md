

### Subdomain Takeover

We gain control over a subdomain of target organisation due to misconfiguration
This happens when a subdomain points to a webservice that has been deleted or is no longer under the control of that organisation but the DNS entry CNAME record still exists

Example : blog.example.com  ---> 3rd party bloggin service

Now is the organisation decides to stop using that blogging service and deletes its account 
they often forget to remove the DNS entry point to it. Now create an account on 3rd paty bloggin service and clain the address that blog.example.com is pointing to.

Anyone visiting blog.example.com will be served the contents provided by us

Here blog.example.com is a CNAME record

`blog.example.com` is configured as a CNAME record pointing to a domain provided by the third-party blogging service.

---

### Open Redirects 

The url of the lab when we click any option

```sh
http://localhost/labs/r0x01_script.php?id=1&return_url=./r0x01.php
```

there is a return URL

***In Open Redirect and SSRF, look for URL's or partial URL's***

To exploit it, just update return URL to malicious webserver or the page you want the victim to go to

When they click back, they will be taken to the return URL provided by us

```sh
http://localhost/labs/r0x01_script.php?id=1&return_url=https://google.com
```


---

### Vulnerable Components


Find these using wappalyzer, builtwith or dev tools and find there version then check for CVE's for these

Automate for these since takes a lot of time
