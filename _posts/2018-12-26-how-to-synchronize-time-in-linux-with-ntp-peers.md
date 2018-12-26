---
layout: post
title: How To Synchronize Time in Linux with NTP Peers
comments: true
categories: ["tips", "linux"]
tags: ["tips", "linux"]
---

There are many different services such as Kerberos that depend on the time of a Linux system being accurate in order to function correctly. It is therefore important to ensure that system time is synchronized with an external source so that it can be kept accurately up to date, this is done with the network time protocol (NTP).

Here we are going to cover how to configure chronyd or ntpd in Linux to connect to an NTP server and keep time in sync. We only want to use one of these at a time however, having both running at once is not a good idea and may cause conflicts.

Note: These examples are based on the CentOS 7 operating system so steps may vary slightly for other Linux distributions. In this version chronyd is installed by default, however we will still cover the older ntpd for completeness as this is still widely used. Here we are concerned with configuring NTP clients rather than an NTP server.

### A Quick NTP Primer
Before we get into the details on how to configure NTP here is some basic information on how NTP works.

Understanding the Hardware and System Clocks
Your Linux system will generally have two clocks, a hardware clock/real time clock (RTC) and a system clock.

The hardware clock is physically present and continues to run from battery power even if the system is not plugged into a power source, this is how the time stays in place when there is no power available. As the Linux system boots up it will read time from the hardware clock, this initial time is then passed to the system clock.

The system clock runs in the kernel and after getting its initial time from the hardware clock it will then synchronize with an NTP server to become up to date.

We can manually synchronize the hardware clock to the system clock if required, this would generally only be required if there was no NTP server available.
<pre>
hwclock --hctosys
</pre>

We can also reverse the process and synchronize the system clock to the hardware clock.

<pre>
hwclock --systohc
</pre>

In CentOS 7 by default chronyd will update the hardware clock with NTP every 11 minutes, in previous versions of the OS this was only done at shutdown/reboot. This configuration is defined by ‘rtcsync’ in /etc/chrony.conf

The hwclock command can also be used to display the current time of the hardware clock as shown below.

<pre>
[root@centos7 ~]# hwclock
Wed 26 Dec 2018 07:55:56 PM +07  -0.856997 seconds
</pre>

The date command can be used to view system time, however in CentOS 7 this has generally been superseded by the timedatectl command, discussed later.

<pre>
[root@centos7 ~]# date
Wed Dec 26 19:33:09 +07 2018
</pre>

### Understanding Stratum

NTP servers work based on a layered hierarchy referred to as stratum, starting at stratum 0. Stratum 0 are the highly exact time sources such as atomic clocks or GPS clocks, these are our reference time devices. Stratum 1 are the computers that synchronize with the stratum 0 sources, these are highly accurate NTP servers. Stratum 2 servers then get their time from the stratum 1 servers, while stratum 3 servers synchronize with stratum 2 sources.

Essentially stratum n+1 will synchronize against stratum n, the highest limit is 15, while 16 refers to a device that is not synchronized. There are plenty of publicly available stratum 1 servers available on the Internet for use. It is generally recommended that you synchronize with a time source higher in the hierarchy, for instance synchronizing time against a stratum 1 server will be considered more reliable than using a stratum 4 server.

### Firewall Rules
By default NTP uses UDP port 123, so if you are connecting over the Internet to an external NTP server ensure that outbound UDP 123 traffic is allowed out to the NTP server specified in your configuration. Normally by default all outbound traffic is allowed so this should not be a problem. Public NTP servers on the Internet should already be configured to accept inbound NTP traffic.

If you are instead running your own local NTP server within your own network you will need to ensure that your servers can connect inbound to the NTP server on UDP port 123, which can be done by running the following command on your NTP server if firewalld is in use, which is the default in CentOS 7.

> firewall-cmd --add-service ntp
Now let’s take a look at configuring NTP with either chronyd or ntpd.

### Chrony

#### Installation

By default in CentOS 7 chrony is the default NTP client/server so it should already be installed if you’re also using this operating system, otherwise you can install it if required as below.

> yum install chrony -y

Once installed we want to make sure that the chronyd service starts up automatically on boot so that we can maintain accurate time.

> systemctl enable chronyd

By default after installing the package the service will not be running, start chronyd if it’s not already running. Once the service starts it will automatically begin synchronizing time against the NTP servers defined in the configuration file.

> systemctl start chronyd

See our guide on managing systemd services with systemctl if you need further information on this.

### Configuration

The configuration for chrony is stored in the /etc/chrony.conf file. The chrony package includes both chronyd and chronyc. Chronyd is the daemon that is actively running and synchronizing with an NTP server, while chronyc is a command line tool used for making various adjustments.

At the top of the configuration file the default NTP servers are specified, in my test server they appear as below. We can define a preferred NTP server by placing “prefer” at the end of one of the server configuration lines.

<pre>
[root@centos7 ~]# cat /etc/chrony.conf
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst
...
</pre>

If you have your own local NTP server you can modify the configuration to use this instead, be sure to reload the service afterwards to apply the changes. The NTP servers may also be retrieved by DHCP configuration, this can be disabled by specifying PEERDNS=no in the network configuration.

### Management
With chrony running we can perform various management tasks. In CentOS 7 as part of systemd we get access to the new timedatectl command which allows us to manage various time settings including NTP.

Below is an example output of the timedatectl command, it shows the current system time as well as hardware clock (RTC) time, current timezone, if NTP is enabled and synchronized as well upcoming DST changes.

<pre>
[root@centos7 ~]# timedatectl
      Local time: Tue 2015-09-15 22:24:11 AEST
  Universal time: Tue 2015-09-15 12:24:11 UTC
        RTC time: Tue 2015-09-15 12:24:03
        Timezone: Australia/Sydney (AEST, +1000)
     NTP enabled: yes
NTP synchronized: no
 RTC in local TZ: no
      DST active: no
 Last DST change: DST ended at
                  Sun 2015-04-05 02:59:59 AEDT
                  Sun 2015-04-05 02:00:00 AEST
 Next DST change: DST begins (the clock jumps one hour forward) at
                  Sun 2015-10-04 01:59:59 AEST
                  Sun 2015-10-04 03:00:00 AEDT

</pre>

The timedatectl command can also be used to enable and disable NTP with set-ntp, it will enable or disable either chronyd or ntpd depending on the service that is in use. In the example below we disable NTP and then enable it again which also triggers it to synchronize with the NTP servers.

<pre>
[root@centos7 ~]# timedatectl set-ntp 0
[root@centos7 ~]# timedatectl | grep NTP
     NTP enabled: no
     NTP synchronized: no

[root@centos7 ~]# timedatectl set-ntp 1
[root@centos7 ~]# timedatectl
     NTP enabled: yes
     NTP synchronized: yes
</pre>

Time synchronization can also be forced with the below chronyc commands.

<pre>
[root@centos7 ~]# chronyc -a 'burst 4/4'
200 OK
200 OK

[root@centos7 ~]# chronyc -a makestep
200 OK
200 OK
</pre>

Chronyc can also be used to view detailed NTP specific information as demonstrated below.

<pre>
[root@centos7 ~]# chronyc tracking
Reference ID    : 203.173.10.97 (203-173-10-97.perm.iinet.net.au)
Stratum         : 4
Ref time (UTC)  : Tue Sep 15 11:54:57 2015
System time     : 0.000043310 seconds slow of NTP time
Last offset     : 0.000002876 seconds
RMS offset      : 0.000330570 seconds
Frequency       : 10.954 ppm slow
Residual freq   : 0.002 ppm
Skew            : 0.052 ppm
Root delay      : 0.025731 seconds
Root dispersion : 0.046666 seconds
Update interval : 1036.5 seconds
Leap status     : Normal
</pre>

We can also view all NTP servers that chronyc is synchronizing with.

<pre>
[root@centos7 ~]# chronyc sources
210 Number of sources = 4
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^* 203-173-10-97.perm.iinet.     3  10   377   863  -4849us[-4846us] +/-   91ms
^+ b.pool.ntp.uq.edu.au          2  10   377   771  +4122us[+4122us] +/-  116ms
^+ 0.time.itoc.com.au            2  10   377   717  +1935us[+1935us] +/-   83ms
^+ saul.foodworks.com.au         2  10   377   652  +1906us[+1906us] +/-   86ms
</pre>

Further information on these sources can then be viewed.

<pre>
[root@centos7 ~]# chronyc sourcestats
210 Number of sources = 4
Name/IP Address            NP  NR  Span  Frequency  Freq Skew  Offset  Std Dev
==============================================================================
203-173-10-97.perm.iinet.  15   9  241m     -0.293      0.307  -6664us  1336us
b.pool.ntp.uq.edu.au       13   9  206m      0.109      0.054  +4457us   150us
0.time.itoc.com.au          7   4  103m     -0.093      0.058  +1906us    42us
saul.foodworks.com.au       6   3   85m      0.244      0.965  +2418us   350us
</pre>

Note that you can also specify the -v flag when running these commands for even further detailed and verbose information.

The time can also be configured through the graphical user interface by installing the `system-config-date` package via yum and then running the `system-config-date` command.

Security Options
By default chronyd will only accept commands from chronyc from the local server, however we can change this to allow remote changes if required. To allow external chronyc access modify the /etc/chrony.conf file as below.

> cmdallow 172.16.0.0/24

This will allow chronyc initiated from a remote host in the 172.16.0.0/24 network range access to our server and perform changes similar to directly editing the /etc/chrony.conf file, this may be useful if you want to be able to remotely manage time on a number of servers. Additionally the default ‘bindcmdaddress’ entries which set chronyd to listen only on localhost should be removed to allow chronyd to listen on all available network interfaces, the chronyd service should then be restarted to apply these changes.

Note that chronyd listens on UDP port 323 for chronyc connections, so firewall rules may need to be put in place to allow the traffic through.

While this locks down access to chronyd from an IP address or range of IP addresses it does not actually perform any authentication, potentially allowing anyone to use chronyc to run commands which is insecure. We can configure a password to limit chronyc access, for further information see the documentation here.

### NTPD

#### Installation
While the NTP daemon is older than chronyd it is still widely used, by default it is not installed in CentOS 7 so you will first need to install it. You should check if chrony is already installed and perhaps disable the service or remove the package to prevent any conflicts. Ntpd can be installed as shown below.

> yum install ntp -y

Once installed we want to make sure that the ntpd service starts up automatically on boot so that we can maintain accurate time.

> systemctl enable ntpd

By default after installing the package the service will not be running, start ntpd if it’s not already running.

> systemctl start ntpd

See our guide on managing systemd services with systemctl if you need further information on this.

#### Configuration
The configuration for ntpd is stored in the /etc/ntp.conf file. This file specifies the NTP servers to synchronize with in the same format as chrony, below is an example of the default configuration that already exists out of the box after installing ntpd.

<pre>
[root@centos7 ~]# cat /etc/ntp.conf
...
server 0.centos.pool.ntp.org iburst
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst
...
</pre>

This default configuration is enough for the server to start synchronizing time with NTP.

#### Management
With ntpd we can perform various management tasks. The ntpq command allows us to query and monitor the NTP daemon, ntpd. For instance we can view information about known NTP peers in a similar way to the ‘chronyc sources’ command with chrony.

<pre>
[root@centos7 ~]# ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
+node02.au.verbn 210.9.192.50     2 u   15   64  177    2.280   -0.791   8.727
-cachens2.onqnet 3.109.212.154    3 u    7   64  177   13.818    0.138   9.007
*hosting.wireles 203.35.83.242    2 u   14   64  177    1.089  -11.553   9.183
+ns2.tti.net.au  203.35.83.242    2 u   14   64  177   14.627   -4.516   9.209
</pre>

The ntpstat command can be used to quickly show the time synchronization status.

<pre>
[root@centos7 ~]# ntpstat
synchronised to NTP server (203.23.237.200) at stratum 3
   time correct to within 253 ms
   polling server every 64 s
</pre>

The ntpdate command has been retired but is still available to set the date and time via NTP, the command below forces a synchronization to the time server specified, in this case pool.ntp.org.

<pre>
[root@centos7 ~]# ntpdate pool.ntp.org
20 Sep 18:53:12 ntpdate[28117]: adjust time server 125.255.139.115 offset 0.014167 sec
</pre>
This functionality has been replaced by ‘ntpd -q’. By default this will not update the hardware clock, to do that edit the /etc/sysconfig/ntpdate file and add “SYNC_HWCLOCK=yes” which is set to no by default, this will ensure the hardware clock is updated after a successful nptdate. This should also allow the system clock to automatically update the hardware clock every 11 minutes.

#### Difference Between Chronyd and Ntpd
Now that we know how to work with both chronyd and ntpd which should you use? In general it doesn’t really matter that much and sticking with the default in your operating system will usually be fine in most instances. With that in mind here are some differences between chronyd and ntpd which may help you decide which to use.

Linux systems that are not permanently online or connected to the network take longer to adjust their system clocks with ntpd as lots of small corrections need to be made. Typically adjustments begin within the first few milliseconds of system boot, however acceptable accuracy may take anywhere from 10 seconds during a warm restart to a number of hours depending on the operating environment and hardware, variables like temperature start to matter and affect the hardware clock. Chronyd is capable of adjusting the time much faster so is better used in systems that will not be continually online and synchronizing with an NTP server.
Chronyd works better than ntpd when NTP servers are only intermittently available, this is because ntpd needs regular polling of the reference time servers for best results. For example if network connectivity is poor chronyd may be a better option than ntpd.
Chronyd supports isolated network functionality whereby the only available way of correcting time is by manual update. This would be useful in a secure network with no access to an NTP server, time is manually entered in and chronyd is capable of examining the errors corrected at different updates to estimate the best accurate time.
Chronyd usually synchronizes time faster and with better accuracy than ntpd.
Ntpd supports NTP version 4 (RFC 5905) while chronyd supports NTP version 3 (RFC 1305), but is compatible with version 4. Basically chronyd does not currently fully support the v4 protocol like ntpd.
To summarize the differences, chronyd should be considered for use on Linux systems that may be frequently suspended, powered down, or otherwise intermittently disconnected from the network. Chronyd is also capable of synchronizing faster than ntpd, however ntpd has better support for version 4 of NTP. Ntpd should be considered for systems that remain permanently up and running, however even then the benefits do not seem to be as great as those provided by chronyd.

#### Summary
As shown synchronizing a Linux system with NTP is essentially as simple as installing and running either the chronyd or ntpd service. It is important to keep a Linux systems time accurately updated to prevent problems associated with out of sync time.


### References:
1. [https://www.rootusers.com/how-to-synchronize-time-in-linux-with-ntp-peers/](https://www.rootusers.com/how-to-synchronize-time-in-linux-with-ntp-peers/)