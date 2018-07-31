---
layout: post
title:  "Time Synchronisation"
tags: [linux, unix, server, sysadmin, systems]
date: 2018-08-01 00:28:01 +0530
created_at: 2017-11-01 00:23:55 +0530
updated_at: 2018-08-01 00:28:01 +0530
categories: til
---
# About
Clock syncronisation between computer systems is must to maintain the accuracy of data being recorded on them. The Network Time Protocol(NTP) is widely used to synchronise computer clocks. 

## ntpdate 
ntpdate - set the date and time via NTP

ntpdate sets the local date and time by polling the Network Time Protocol (NTP) server(s) given as the server arguments to determine the correct time. It must be run as root on the local host.

```bash
$ ntpdate pool.ntp.org

# e.g: run ntpdate via cron
$ sudo crontab -l
0 * * * * /usr/sbin/ntpdate pool.ntp.org

```
Note: ntpdate is deprecated long ago.

## ntpd
ntpd - Network Time Protocol (NTP) Daemon

The ntpd program is an operating system daemon that synchronizes the system clock to remote NTP time servers or local reference clocks.

```bash
$ sudo apt install ntp

# ntpq - standard NTP query program
$ ntpq
ntpq> lpeers
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 139.59.43.68    217.114.59.66    3 u   28   64    1    9.419  1191.94   0.000
 fwdns2.vbctv.in 202.73.57.107    3 u   27   64    1   39.436  1200.61   0.000
 golem.canonical 145.238.203.14   2 u   26   64    1  140.603  1188.74   0.000
ntpq> peers
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 139.59.43.68    217.114.59.66    3 u   34   64    1    9.419  1191.94   0.000
 fwdns2.vbctv.in 202.73.57.107    3 u   33   64    1   39.436  1200.61   0.000
 golem.canonical 145.238.203.14   2 u   32   64    1  140.603  1188.74   0.000
ntpq> opeers
     remote           local      st t when poll reach   delay   offset    disp
==============================================================================
 139.59.43.68    10.0.2.15        3 u   50   64    1    9.419  1191.94 7937.50
 fwdns2.vbctv.in 10.0.2.15        3 u   49   64    1   39.436  1200.61 7937.50
 golem.canonical 10.0.2.15        2 u   48   64    1  140.603  1188.74 7937.50
ntpq> readlist
associd=0 status=c012 leap_alarm, sync_unspec, 1 event, freq_set,
version="ntpd 4.2.6p5@1.2349-o Wed Jul 12 12:22:58 UTC 2017 (1)",
processor="x86_64", system="Linux/3.13.0-116-generic", leap=11,
stratum=16, precision=-23, rootdelay=0.000, rootdisp=0.915, refid=INIT,
reftime=00000000.00000000  Mon, Jan  1 1900  0:00:00.000,
clock=dea787d9.c369cb98  Thu, May 17 2018  4:47:21.763, peer=0, tc=3,
mintc=3, offset=0.000, frequency=-371.340, sys_jitter=0.000,
clk_jitter=0.000, clk_wander=0.000
ntpq> quit

# Print a list of the peers
$ sudo ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 139.59.43.68    217.114.59.66    3 u   64   64    3   12.469  1160.21  31.737
 fwdns2.vbctv.in 202.73.57.107    3 u    2   64    7   40.591  1142.75  43.626
 golem.canonical 145.238.203.14   2 u   64   64    3  137.588  1164.11  24.625

# sync
$ sudo ntpd -q

# sync with debug
$ sudo ntpd -d -d
```


## timedatectl
timedatectl - Control the system time and date

timedatectl is part of systemd-services package on ubuntu. It may be used to query and change the system clock and its settings. 


```bash
$ lsb_release -d
Description:    Ubuntu 14.04.5 LTS

$ dpkg -S timedatectl
systemd-services: /usr/share/man/man1/timedatectl.1.gz
systemd-services: /usr/share/bash-completion/completions/timedatectl
systemd-services: /usr/bin/timedatectl

# host without ntp synchronisation enabled
$ timedatectl                                      
      Local time: Thu 2018-05-17 01:07:00 UTC
  Universal time: Thu 2018-05-17 01:07:00 UTC
        Timezone: Etc/UTC (UTC, +0000)
     NTP enabled: n/a
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a

# install ntp package
$ sudo apt install ntp

$ timedatectl 
      Local time: Thu 2018-05-17 01:07:20 UTC
  Universal time: Thu 2018-05-17 01:07:20 UTC
        Timezone: Etc/UTC (UTC, +0000)
     NTP enabled: n/a
NTP synchronized: yes
 RTC in local TZ: no
      DST active: n/a

$ sudo ntpq -p

# reboot and check if you see NTP synchronized: no
$ timedatectl 
      Local time: Thu 2018-05-17 01:08:05 UTC
  Universal time: Thu 2018-05-17 01:08:05 UTC
        Timezone: Etc/UTC (UTC, +0000)
     NTP enabled: yes
NTP synchronized: yes
 RTC in local TZ: no
      DST active: n/a
```

* [Network Time Protocol Version 4: Protocol and Algorithms Specification](https://tools.ietf.org/html/rfc5905)
* [Time Synchronisation with NTP](https://help.ubuntu.com/lts/serverguide/NTP.html)
* [ntpdate - set the date and time via NTP](http://doc.ntp.org/4.1.1/ntpdate.htm)
* [ntpq - standard NTP query program](http://doc.ntp.org/4.1.0/ntpq.htm)
* [Network Time Protocol](https://en.wikipedia.org/wiki/Network_Time_Protocol)
* [timedatectl — Control the system time and date](https://www.freedesktop.org/software/systemd/man/timedatectl.html)

