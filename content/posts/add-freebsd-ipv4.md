---
title: "Adding static IPv4 in Freebsd"
author: ["Dhananjay"]
date: 2021-08-06T14:02:00+05:30
tags: ["freebsd", "ip", "ipv4", "how-to", "networking"]
draft: false
weight: 2006
---

This is quick how-to guide on adding to FreeBSD machine.


## Set permanent IP {#set-permanent-ip}

-   Open `/etc/rc.conf`

<!--listend-->

```shell
vi /etc/rc.conf
```

-   Search for `ifconfig_xn0` (`xn0` is interface name) and edit its contents to reflect your IP.

<!--listend-->

```shell
hostname="freebsd"
ifconfig_xn0="inet 192.168.10.1 netmask 255.255.250.0"
```

-   Run the `/etc/netstart` script to update configuration.

<!--listend-->

```shell
./etc/netstart
```


## Set temporary IP {#set-temporary-ip}

NOTE: IP will be reverted after reboot.

-   Get the interface name using `ifconfig -a` (you don't want `lo` interface)

<!--listend-->

```shell
$ ifconfig -a
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
     ...
xn0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
     ...
```

-   Set the ip for `xn0` using following command.

<!--listend-->

```shell
$ ifconfig xn0 inet 192.168.10.1
```
