There are many more pertinent modes we need to know for our wireless interfaces when we conduct wi-fi penetration testing. After all, each mode is responsible for different capabilities and roles when it comes down to the hierarchy of wi-fi communications. In this section, we will explore each of these separate modes, what they do, and how we can test our interface for their capabilities.

---

## Managed Mode

Managed mode is when we want our interface to act as a client or a station. In other words, this mode allows us to authenticate and associate to an access point, basic service set, and others. In this mode, our card will actively search for nearby networks (APs) to which we can establish a connection.

Pretty much in most cases, our interface will default to this mode, but suppose we want to set our interface to this mode. This could be helpful after setting our interface into monitor mode. We would run the following command.

```shell
Kairos@htb[/htb]$ sudo ifconfig wlan0 down
Kairos@htb[/htb]$ sudo iwconfig wlan0 mode managed
```

Then, to connect to a network, we could utilize the following command.


```shell
Kairos@htb[/htb]$ sudo iwconfig wlan0 essid HTB-Wifi
```

Then, to check our interface, we can utilize the `iwconfig` utility.

```shell
Kairos@htb[/htb]$ sudo iwconfig
```

![[iwconfig.png]]

## Ad-hoc Mode

Secondarily, we could act in a decentralized approach. This is where ad-hoc mode comes into play. Essentially this mode is peer to peer and allows wireless interfaces to communicate directly to one another. This mode is commonly found in most residential mesh systems for their backhaul bands. That is their band that is utilized for AP-to-AP communications and range extension. However, it is important to note, that this mode is not extender mode, as in most cases that is actually two interfaces bridged together.

To set our interface into this mode, we would run the following commands.

```shell
Kairos@htb[/htb]$ sudo iwconfig wlan0 mode ad-hoc
Kairos@htb[/htb]$ sudo iwconfig wlan0 essid HTB-Mesh
```

Then, once again, we could check our interface with the `iwconfig` command.

```shell
Kairos@htb[/htb]$ sudo iwconfig
```

![[ad-hoc.png]]

## Master Mode

On the flip side of managed mode is master mode (access point/router mode). However, we cannot simply set this with the `iwconfig` utility. Rather, we need what is referred to as a management daemon. This management daemon is responsible for responding to stations or clients connecting to our network. Commonly, in wi-fi penetration testing, we would utilize hostapd for this task. As such, we would first want to create a sample configuration.

```shell
Kairos@htb[/htb]$ nano open.conf

interface=wlan0
driver=nl80211
ssid=HTB-Hello-World
channel=2
hw_mode=g
```

This configuration would simply bring up an open network with the name HTB-Hello-World. With this network configuration, we could bring it up with the following command.

```shell
Kairos@htb[/htb]$ sudo hostapd open.conf
```

![[hostapd.png]]

In the above example, hostapd brings our AP up, then we connect another device to our network, and we should notice the connection messages. This would indicate the successful operation of the master mode.

## Mesh Mode

Mesh mode is an interesting one in which we can set our interface to join a self-configuring and routing network. This mode is commonly used for business applications where there is a need for large coverage across a physical space. This mode turns our interface into a mesh point. We can provide additional configuration to make it functional, but generally speaking, we can see if it is possible by whether or not we are greeted with errors after running the following commands.

```shell
Kairos@htb[/htb]$ sudo iw dev wlan0 set type mesh
```

Then we can check our interface once again with the `iwconfig` utility.

```shell
Kairos@htb[/htb]$ sudo iwconfig
```

![[mesh.png]]

## Monitor Mode

Monitor mode, also known as promiscuous mode, is a specialized operating mode for wireless network interfaces. In this mode, the network interface can c*apture all wireless traffic within its range*, regardless of the intended recipient. Unlike normal operation, where the interface only captures packets addressed to it or broadcasted, monitor mode enables comprehensive network monitoring and analysis.

Enabling monitor mode typically requires *administrative privileges* and may vary depending on the operating system and wireless chipset used. Once enabled, monitor mode provides a powerful tool for understanding and managing wireless networks.

First we would need to bring our interface down to avoid a device or resource busy error.

```shell
Kairos@htb[/htb]$ sudo ifconfig wlan0 down
```

Then we could set our interface's mode with iw {interface name} set {mode}

```shell
Kairos@htb[/htb]$ sudo iw wlan0 set monitor control
```

Then we can bring our interface back up.

```shell
Kairos@htb[/htb]$ sudo ifconfig wlan0 up
```

Finally, to ensure that our interface is in monitor mode, we can utilize the `iwconfig` utility.

```shell
Kairos@htb[/htb]$ iwconfig
```

![[monitor.png]]

Overall, it is important to make sure our interface supports whatever mode is pertinent to our testing efforts. If we are attempting to exploit WEP, WPA, WPA2, WPA3, and all enterprise variants, we are likely sufficient with just monitor mode and packet injection capabilities. However, suppose we were trying to achieve different actions we might consider the following capabilities.

1. `Employing a Rogue AP or Evil-Twin Attack:` - We would want our interface to support master mode with a management daemon like hostapd, hostapd-mana, hostapd-wpe, airbase-ng, and others.
2. `Backhaul and Mesh or Mesh-Type system exploitation:` - We would want to make sure our interface supports ad-hoc and mesh modes accordingly. For this kind of exploitation we are normally sufficient with monitor mode and packet injection, but the extra capabilities can allow us to perform node impersonation among others.

In the next section, we will dive into the essentials of *Aircrack-ng*, a powerful suite of tools designed for wireless network security assessments. We will cover the core functionalities of Aircrack-ng, including how to use it for capturing packets, analyzing network traffic, and cracking WEP and WPA/WPA2 encryption keys. This exploration will provide you with a solid foundation for using Aircrack-ng tools in your own security evaluations and network assessments.


---
## Questions

1.  How many interface modes are available? (Answer in digit format: e.g., 3)
	Answer: *5*

**Next step:** [[Introduction to Aircrack-ng]]