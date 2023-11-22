
Always the methodology, not the tool

In figerprinting technologies, if web server is Microsoft IIS, maybe try with extesion .asp, .aspx, if it is Debian or Ubuntu, try with extension .php

---

### Fingerprinting Web Technologies

Example website : azena.com

1) Builtwith

Just type the name of website

2) Wappalyzer

Firefox and chrome extension

3) cURL (client URL)

Command line tool, fingerprinting from HTTP headers

```sh
curl -I azena.com
```


4) nmap (Network Mapper

```sh
nmap -A -p80,443 azena.com
```



---


### Directory Enumeration and Brute Forcing/Directory Busting


1) ffuf

Non-recursive

```sh
ffuf -w wordLists:FUZZ -u url/FUZZ
```

Recursive

```sh
ffuf -w wordlists:FUZZ -u url/FUZZ -recursion
```


Filter out 404 status

```sh
ffuf -w wordlists:FUZZ -u url/FUZZ -recursion -fc 404
```


2) dirb

```sh
dirb url /usr/share/wordlists/dirb/common.txt
```

***It automatically scans recursively***

3) dirbuster

Has a GUI


---


### Subdomain Enumeration

1) Google Dorking

```sh
site:azena.com -www filetype:pdf
```


2) crt.sh

In this website, use % for wildcard, example : %.azena.com

This is based on certificate ID


3) subfinder

```sh
subfinder -d azena.com -o azenaOutput.txt
```

-d : domain name
-o : output to a file


4) assetfinder

```sh
assetfinder azena.com | grep azena | sort -u |tee assetFinderOut.txt
```


-u : only unique lines are retained in the output

5) amass

```sh
amass enum -d azena.com | amassOut.txt
```

-d : domain


##### To find alive subdomains

```sh
cat azenaFinal.txt | grep azena.com | httprobe -prefer-https | grep https | tee azenaAlive.txt
```

6) gowitness

```sh
gowitness azenaAlive.txt -P azenapics -no-http 
```

-P : directory for storing pictures


---

### BurpSuite


It is a web proxy, we can proxy our traffic through this tool

Go to Proxy -> Proxy Settings -> Set up proxy listener on any free port

Tool : FoxyProxy, to proxy traffic through burp

Once your proxy is on

Go to
\http://burp

Download the CA certificate

Go to browser Settings -> Privacy and Security -> View Certificates -> Click on Import ->Select the downloaded Certificate -> Select both checkboxes for Trust, accept it


To set Scope Settings

Target Tab-> Scope Settings -> Check 'Use Advanced Scope Control' box -> 'Host or IP' : Target 





