---
layout: post
title: Apache Configuration File Tips
comments: true
categories: ["tips", "linux"]
tags: ["tips", "linux"]
---
The default Apache settings that cPanel sets upon install are definitely something that can be improved on. With a few small tweaks, the efficiency with which Apache runs with can be greatly improved.

Please noted: This article assumes that you are using a Linux server running Apache and cPanel or Plesk, and that you are familiar with editing files from the command line.


To start, open the Apache configuration file and finding the directives section. On a cPanel server, it will be located in /usr/local/apache/conf/. On a Plesk server, it will be in /etc/httpd/conf/. If you are using vi or vim: once you open the file, you can find the directives by scrolling through the file, or by typing forward-slash ‘/’ and typing the exact string that you are looking for (search is case specific).

<pre>
[root@host /] vim /usr/local/apache/conf/httpd.conf
</pre>

or

<pre>
[root@host /] vim /etc/httpd/conf/httpd.conf
</pre>

This list is a composite of the settings we will be reviewing from fresh install on a cPanel server:
`
Timeout 300
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 15
MinSpareServers 5
MaxSpareServers 10
StartServers 5
MaxClients 150
MaxRequestsPerChild 0
`

Please note, the settings that we will review in this article are by no means a complete list of tweakable options in the Apache configuration file. The settings we will be focusing on are the ones that control how Apache handles webpage requests.

## Timeout

`Timeout 300`

Usually this value doesn’t require editing and a default of 300 is sufficient. Lowering the ‘Timeout’ value will cause a long running script to terminate earlier than expected.

On virtualized servers like VPS servers, lowering this value to 100 can help improve performance.

 

## KeepAlive

`KeepAlive On`

This setting should be “On” unless the server is getting requests from hundreds of IPs at once.

High volume and/or load balanced servers should have this setting disabled (Off) to increase connection throughput.

 

## MaxKeepAliveRequests

`MaxKeepAliveRequests 100`

This setting limits the number of requests allowed per persistent connection when KeepAlive is on. If it is set to 0, unlimited requests will be allowed.

It is recommended to keep this value at 100 for virtualized accounts like VPS accounts. On dedicated servers it is recommended that this value be modified to 150.

 

## KeepAliveTimeout

`KeepAliveTimeout 15`

The number of seconds Apache will wait for another request before closing the connection. Setting this to a high value may cause performance problems in heavily loaded servers. The higher the timeout, the more server processes will be kept occupied waiting on connections with idle clients.

It is recommended that this value be lowered to 5 on all servers.

 

## MinSpareServers

`MinSpareServers 5`

This directive sets the desired minimum number of idle child server processes. An idle process is one which is not handling a request. If there are fewer spareservers idle then specified by this value, then the parent process creates new children at a maximum rate of 1 per second. Setting this parameter to a large number is almost always a bad idea.

Liquidweb recommends adjusting the value for this setting to the following:
<pre>
Virtualized server, ie VPS 5

Dedicated server with 1-2GB RAM 10

Dedicated server with 2-4GB RAM 20

Dedicated server with 4+ GB RAM 25

MaxSpareServers

MaxSpareServers 10
</pre>

The MaxSpareServers directive sets the desired maximum number of idle child server processes. An idle process is one which is not handling a request. If there are more than MaxSpareServers idle, then the parent process will kill off the excess processes.

The MaxSpareServers value should be set as double the value that is set in MinSpareServers.

 

## StartServers

`StartServers 5`

This directivesets the number of child server processes created on startup. This value should mirror what is set in MinSpareServers.

 

## MaxClients

`MaxClients 150`

This directive sets the limit on the number of simultaneous requests that will be served. Any connection attempts over the specified limit will be queued. Once a process is freed at the end of a different request, the queued connection will then be served.

For virtualized servers such as VPS accounts, it is recommended to keep this value at 150. For all dedicated servers the recommended value for this setting is 250.

## MaxRequestsPerChild

`MaxRequestsPerChild 0`

This directive sets the limit on the number of requests that an individual child server process will handle. After the number of requests reaches the value specified, the child process will die. When this value is set at 0, then the process will never expire.

### References
1. [https://www.liquidweb.com/kb/apache-optimization/](https://www.liquidweb.com/kb/apache-optimization/)