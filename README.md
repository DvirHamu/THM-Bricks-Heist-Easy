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



