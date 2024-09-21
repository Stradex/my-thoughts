---
date: 2024-09-20
draft: false
params:
  author: Stradex
title: LAMP & Laravel stack optimization.
---

*(Post in progress)*

## LAMP & Laravel Stack optimization.

Hi and welcome to another post of mine about tech stuff.
This time I want to talk about something very related to my current work: LAMP, Laravel and optimization for high traffic servers.

## Personal word of advice.

Word of advice from personal experience before diving a lot into the trap of obsession related to optimize everything to make it work with less hardware and bandwith possible.
In where I live, Argentina, I have a phrase for that called "ser un pajero". Don't be a "pajero", think before optimizing a hammer when what you need it's a screwdriver.

* **Load balancing your apps**: Yes, we all want things to work as fast and with less resources as possible, and we sometimes have the feeling that "this should work with just one server for this traffic, I should optimize it to work", but optimization takes time, time is money and clients can start losing money while you are trying to optimize a PHP script to execute from 0.05 seconds to 0.045 seconds. Sometimes the fast solution is just adding another server and do some horizontal scaling with a load balancer for requests. You can set up a Load Balancer server, use tools from Cloudfare, Digital Ocean, etc...

* **Put your database outside from your webapp disk**: You NEVER want your database to be sharing the same disk you are using for your web app. Don't spend hs and days trying to optimize your queries, optimizing your MySQL or MariaDB engine, etc... without first putting your database in another disk, and if possible, in another server in a shared local network between your servers. 

* **When possible, pick horizontal scaling over vertical scaling**: In personal experience, vertical scaling (like going from a VPS to a giant Dedicated Server, etc..) can help only up to a point. Yes, it can rescue you from an urgent need when a client is knocking your door asking you why your app is running so slow while holding an axe in a Berserk rage's state. But usually horizontal scaling works far better. One big point of horizontal scaling is that you can distribute easily one machine as CDN for static assets, another for the database, another for your web app server core code, etc.. and you can even do horizontal scaling for each one like: Having two or more VPS for your CDNs' servers, having two or more VPSs for your DB, etc.. etc... And, in top of that, usually most of Hosting prooviders when you get a VPS you get a machine with 1GBPS (or more) of speed for THAT machine, so the more horizontal scaling you do the higher bindwith you can accept in your web app.

* **Think before trying to optimize something**: This should be actually the first point. Before trying to optimize something, think if maybe you are not trying to optimize a hammer because it is not chopping wood as fast as you need. One friend of mine, and a person I consider the greatest developer I've meet in my life, warned me against this sickness of trying to optimize things for not thinking that maybe you are just using the right tool for the job. Another great thing he teached me is that in IT industry it seems that we tend to abstract so much that we lose our capacity to think in a more practical way, we sometimes tend to do things that in any other field, like construction for example, would be considered total nonsense.

All that being said, let's go to the tricks I learned about optimization for the LAMPstack.

## Linux optimization tips for web apps.

I only have experience using Debian-based distros, but you could apply the same ideas to any other distros probably.
This part will be short since I am not expert in linux at all but I learned a few tricks of optimization for networking and high traffic servers.

All the modifications will be done at /etc/sysctl.conf  

### vm.swappiness

This setting controls how intensively the system swaps out pages from RAM to disk swap space. The values go from 0 to 100.
For servers with limited memory, you should set this value high to avoid the server being out of ram and crashing.

But for servers with high available memory, you must consider putting this value really low, like between 0 and 25 for example.

### Recycle time-wait connections.

Many times you will have like ton of time-wait connections in your network, so reusing them can be beneficial in some cases.

```
net.ipv4.tcp_tw_recycle = 0
net.ipv4.tcp_tw_reuse = 1 
```

## Apache optimization for high traffic.

First of all, yes, I know that nginx usually is faster and if possible may you should consider to use it instead of apache. But I believe that, after you read all above, you should consider that the problem is not "if apache or nginx is faster" but "How much time and how messy would be to port my app to nginx, is it worth it?, will it really make so much difference?"

### Chosing the right Apache's MPM.

Usually for high traffic servers, the default prefork mpm will not be ideal. Usually you want to use the worker mpm or event mpm.
I personally use the mpm_worker.

To enable and configure mpm worker you do the following:

1. a2dismod mpm_prefork
2. a2enmod mpm_worker
3. open the /etc/apache2/mods-enabled/mpm_worker.conf file to start configuring you worker.

Most recommend to configure the worker this way [[1]](https://blog.emka.web.id/2022/09/how-to-calculate-perfect-apache-mpm.html):

```
ServerLimit           (Total RAM - Memory used for Linux, DB, etc.) / process size
StartServers          (Number of Cores)
MinSpareThreads       25
MaxSpareThreads       75
ThreadLimit           64
ThreadsPerChild       25
MaxRequestWorkers     (Total RAM - Memory used for Linux, DB, etc.) / process size
MaxConnectionsPerChild   1000
```

### Tweaking Apache configuration.

You usually configure this at /etc/apache2/apache2.conf

```
#
# Timeout: The number of seconds before receives and sends time out.
#
# Personal comment:
# You want to keep this value as low as possible, but make sure not to make the value so long that your web app does not have time to execute the required scripts to generate the view / do what it has to do. Usually point is, you want this value to be a bit higher than your slower script in your server during high traffic conditions.
#
Timeout 100

#
# KeepAlive: Whether or not to allow persistent connections (more than
# one request per connection). Set to "Off" to deactivate.
#
# Personal comment:
# You usually want this ALWAYS ON
#
KeepAlive On

#
# MaxKeepAliveRequests: The maximum number of requests to allow
# during a persistent connection. Set to 0 to allow an unlimited amount.
# We recommend you leave this number high, for maximum performance.
#
# Personal comment:
# Nothing more to add here, Apache description makes it as clear as possible. FOr high traffic servers with enough resources, put this as high as possible but do not go too far from what you need.
#
MaxKeepAliveRequests 500

```
