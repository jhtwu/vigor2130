# Introduction #

2130 is basically a Linux box with OpenWrt software platform.
The most command in Linux or OpenWrt can be used in 2130.

CLI (command-line interface) access via Telnet/SSH ; use the password you set in the Web UI, the default _username/password_ is _admin/admin_ for root.You can also add user account in Web UI >> user.

**Note**: You should change the root password for security issue. Especially when you turn on remote management.

# Internet Connection #

  * **DHCP** mode:
```
    #uci set network.wan.proto=dhcp
    #uci commit
    #/etc/init.d/network restart
```

  * **Static IP** mode: (example)
```
    #uci set network.wan.proto=static
    #uci set network.wan.ipaddr=74.125.115.103
    #uci set network.wan.netmask=255.255.255.0
    #uci set network.wan.gateway=74.125.115.1
    #uci set network.wan.dns=8.8.8.8
    #uci add_list network.wan.dns=8.8.4.4 (2nd dns)
    #uci commit
    #/etc/init.d/network restart
```

  * **PPPoE** mode:
```
    #uci set network.wan.proto=pppoe
    #uci set network.wan.username='myaccount'
    #uci set network.wan.password='mypassword'
    #uci commit
    #/etc/init.d/network restart
```

  * for other settings, you can check the config file by:
```
    # cat /etc/config/network
```


# Wireless #

  * **Set channel & SSID for SSID 1:**
```
    # uci set wireless.ra0.channel=6
    # uci set wireless.@wifi-iface[0].ssid=MyFirstSSID
    # uci commit wireless
    # wifi reload
```

  * **Set WPA\_WPA2 PSK / TKIP or AES for SSID 1**
```
    # uci set wireless.@wifi-iface[0].encryption=pskpsk2
    # uci set wireless.@wifi-iface[0].wpa_crypto=tkipaes
    # uci set wireless.@wifi-iface[0].key=99999999
    # uci commit wireless
    # wifi reload
```

  * For more settings, you can edit the configuration file: /etc/config/wireless


# Diagnostic #

  * **ping:**
```
root@KD52130:~# ping 8.8.8.8 -c 5
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=55 time=64.952 ms
64 bytes from 8.8.8.8: seq=1 ttl=55 time=20.501 ms
64 bytes from 8.8.8.8: seq=2 ttl=55 time=24.042 ms
64 bytes from 8.8.8.8: seq=3 ttl=55 time=23.533 ms
64 bytes from 8.8.8.8: seq=4 ttl=55 time=23.655 ms
--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 20.501/31.336/64.952 ms
root@KD52130:~#	
```


  * **traceroute:**
```
root@KD52130:~# traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 40 byte packets
 1  h254.s98.ts.hinet.net (168.95.98.254)  16.649 ms  20.493 ms  18.901 ms
...
..
root@KD52130:~#	
```

  * **route:**
```
root@KD52130:~# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.2.4      *               255.255.255.255 UH    0      0        0 ppp301
172.17.3.211    *               255.255.255.255 UH    0      0        0 ppp601
168.95.98.254   *               255.255.255.255 UH    0      0        0 ppp0
168.95.98.254   *               255.255.255.255 UH    0      0        0 ipsec0
192.168.15.0    *               255.255.255.0   U     0      0        0 ppp601
192.168.168.0   172.17.3.2      255.255.255.0   UG    0      0        0 br-lan
192.168.12.0    172.17.3.51     255.255.255.0   UG    0      0        0 br-lan
172.17.3.0      *               255.255.255.0   U     0      0        0 br-lan
172.17.1.0      172.17.3.2      255.255.255.0   UG    0      0        0 br-lan
10.10.0.0       172.17.3.80     255.255.0.0     UG    0      0        0 br-lan
172.16.0.0      *               255.255.0.0     U     0      0        0 ppp301
default         h254.s98.ts.hin 0.0.0.0         UG    0      0        0 ppp0
root@KD52130:~#
```


  * **ifconfig:**
```
root@KD52130:~# ifconfig --help
Usage: ifconfig [-a] interface [address]
Configure a network interface
Options:
        [add ADDRESS[/PREFIXLEN]]
        [del ADDRESS[/PREFIXLEN]]
        [[-]broadcast [ADDRESS]] [[-]pointopoint [ADDRESS]]
        [netmask ADDRESS] [dstaddr ADDRESS]
        [hw ether ADDRESS] [metric NN] [mtu NN]
        [[-]trailers] [[-]arp] [[-]allmulti]
        [multicast] [[-]promisc] [txqueuelen NN] [[-]dynamic]
        [up|down] ...

root@KD52130:~#
root@KD52130:~# ifconfig br-lan
br-lan    Link encap:Ethernet  HWaddr 00:50:7F:C7:7B:98
          inet addr:172.17.3.6  Bcast:172.17.3.255  Mask:255.255.255.0
          inet6 addr: 2001:b010:7300:200:250:7fff:fec7:7b98/64 Scope:Global
          inet6 addr: fe80::250:7fff:fec7:7b98/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:14322512 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6585735 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:3763722758 (3.5 GiB)  TX bytes:2866933729 (2.6 GiB)

root@KD52130:~# ifconfig ppp0
ppp0      Link encap:Point-to-Point Protocol
          inet addr:114.34.220.210  P-t-P:168.95.98.254  Mask:255.255.255.255
          inet6 addr: fe80::25d4:f3ea:1956:cafe/10 Scope:Link
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1442  Metric:1
          RX packets:6549254 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12040402 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:3
          RX bytes:2670109757 (2.4 GiB)  TX bytes:3596863758 (3.3 GiB)

root@KD52130:~#
```