---
title: "Static IP in Debian"
header:
  image: /assets/posts/en/static-ip-in-debian/header.jpg
categories:
  - Tutorial
  - PC
  - Linux
lang: en
ref: 35
permalink: /en/static-ip-in-debian/
last_modified_at: 2021-12-15
---

By default, the [_Debian 10_](https://www.debian.org/) installer configures the network via [_DHCP_](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol). This causes the server's _IP_ to change with each reboot unless its _IP_ is reserved in the _Router_. In my case, the network configuration of certain devices I prefer to configure manually on the device itself, and my _Router_ does not allow to reserve more than 8 _IPs_ so it becomes unfeasible to reserve _IPs_ for all devices in my house.

Let's see how to change the network configuration of the server so that it always has the same _IP_. For the moment, you can check what the current configuration is with the command:

```bash
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:e0:4c:0e:5f:07 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.2/24 brd 192.168.1.255 scope global enp2s0
       valid_lft forever preferred_lft forever
    inet6 fe80::2e0:4cff:fe0e:5f07/64 scope link
       valid_lft forever preferred_lft forever
```

Keep the interface whose `inet` complies with the usual range of _IPs_ in your network. In my case `enp2s0`.

## Assigning a static IP

Once the interface has been identified. Open the network configuration file with the command:

```bash
$ sudo nano /etc/network/interfaces
```

The `nano` text editor will be shown with the file more or less as follows:

```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug enp2s0
iface enp2s0 inet dhcp
```

Knowing the interface to modify (in my case `enp2s0`), modify the `dhcp` part to add the manual configuration, and change `allow-hotplug` to `auto` so you don't have to restart the server with every configuration change.

```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto enp2s0
iface enp2s0 inet static
        address 192.168.1.2
        netmask 255.255.255.0
        network 192.168.1.1
        broadcast 192.168.1.255
        gateway 192.168.1.1
```

Change each field to the data corresponding to your network and your interface.

Then save the file with the **F3** key and close the text editor with **F2**.

## Modify the DNS server

If you also want to modify the _DNS_ server used by the device for _DNS_ resolution, execute the following command:

```bash
$ sudo nano /etc/resolv.conf
```

In the text editor, add or modify as many `nameserver` lines as _DNS_ servers you want to use. For example, to use the _Google DNSs_:

```bash
domain Home
search Home
nameserver 1.1.1.1
nameserver 8.8.8.8
```

In my case, I let the _Router_ be in charge of assigning the _DNS_ server that I previously configured on it, so I point to the _Router_'s gateway:

```bash
domain Home
search Home
nameserver 192.168.1.1
```

> If you left the _Domain_ settings empty in the _Debian_ installation, the `domain` and `search` fields may not be set in the file. They are not necessary.

Again, save with F3 and came out with F2.

## Applying changes

To apply the changes, execute these commands with the appropriate interface:

```bash
$ sudo ifdown enp2s0
$ sudo ifup enp2s0
```

## Testing

You can check that the changes have been applied by running the command again:

```bash
$ ip a
```

You will see that the interface is now assigned the _IP_ configured in previous steps.

As for the _DNS_ server. You can execute a simple `ping` command to see if any domain is resolved:

```bash
$ ping -c3 danielmartingonzalez.com
PING danielmartingonzalez.com (185.199.109.153) 56(84) bytes of data.
64 bytes from 185.199.109.153 (185.199.109.153): icmp_seq=1 ttl=57 time=11.2 ms
64 bytes from 185.199.109.153 (185.199.109.153): icmp_seq=2 ttl=57 time=10.9 ms
64 bytes from 185.199.109.153 (185.199.109.153): icmp_seq=3 ttl=57 time=10.8 ms

--- danielmartingonzalez.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 5ms
rtt min/avg/max/mdev = 10.796/10.941/11.157/0.177 ms
```

## Conclusion

Our machine with _Debian_ will now have a **manual network configuration** that will facilitate its management. To do this, you only need to modify two files and restart the network interface. This knowledge will be useful in the future for next articles that will require the device itself to act as a _DNS_ server. But that will be seen later.
