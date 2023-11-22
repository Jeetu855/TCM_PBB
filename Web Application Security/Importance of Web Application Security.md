
![[importance.png]]


Securing web apps(websites) and making sure that they are safe.


---

### Web Application Security Standards & Best Practices


![[security.png]]


Secure code writing

![[standards.png]]


---

### Bug Bounty Hunting vs Penetration Testing


![[pentestvsBB.png]]


In Bug Bounty, impact is everything meanwhile in Penetration testing, each finding is important, so vulnerabilities from low level to critical level.

Pentesting also includes non-impactful findings

![[non-impact.png]]



---

### Phases of Web Application Penetration Testing


![[stepsEngagement.png]]


---

#### Lab Setup

```sh
sudo apt install docker.io
sudo apt install docker-compose
```

To pull the images

```sh
sudo docker-compose up
```

To show all the running containers

```sh
sudo docker ps -a
```


To stop 

```sh
sudo docker stop ContainerID
```



---


### Web Technologies


HTML : Standard markup language used to create web pages, make skeletion of web page
CSS : Make web page usually appealing
JS : High level programming language used to make web pages functional, interactive


Traditional Web Apps : Full page reload for each request
Mordern Web Apps : Only update part of a page on for a request(in things like React)


HTTP : Request-Response Protocol

HTTP Request : Method Path Version
HTTP Response HTTP_Version Response Code

***HTTP is stateless, meaning each request from client to the server is treated as a new standalone request with no memory of prior request that is why mechanisam like cookies are used to maintain state across multiple requests. Uniques cookie given to each user once they are logged in and the server uses that to identify the user.***

DNS : Translates domain name to IP address, similar to a phonebook, we know the persons name and wish to know their phoneNumber
Domains have a heirarchial structure
We have a 
Top level Domain : .com , .edu
Second Level Domain : google, tcm-sec
Subdomain : www, dev

DNS record : A recordm AAAA types, MX, CNAME DNS records for alias of a domain name