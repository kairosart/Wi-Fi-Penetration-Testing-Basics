Monitor mode is a specialized mode for wireless network interfaces, enabling them to capture all traffic within a WiFi range. Unlike managed mode, where an interface only processes frames addressed to it, monitor mode allows the interface to capture every packet of data it detects, regardless of its intended recipient. This capability is invaluable for network analysis, troubleshooting, and security assessments, as it provides a comprehensive view of the network's activity. By enabling monitor mode, users can intercept and analyze packets, detect unauthorized devices, identify network vulnerabilities, and gather comprehensive data on wireless networks. This mode provides a deeper level of insight into the wireless environment, facilitating more effective troubleshooting, security assessments, and performance evaluations.

## Starting monitor mode

Airmon-ng can be used to enable monitor mode on wireless interfaces. It may also be used to kill network managers, or go back from monitor mode to managed mode. Entering the `airmon-ng` command without parameters will show the wireless interface name, driver and chipset.

```shell
Kairos@htb[/htb]$ sudo airmon-ng
```

![[airmon-ng.png]]

We can set the wlan0 interface into monitor mode using the command `airmon-ng start wlan0`.

```shell
Kairos@htb[/htb]$ sudo airmon-ng start wlan0
```

![[airmon-ng_start.png]]

We could test to see if our interface is in monitor mode with the iwconfig utility.

```shell
Kairos@htb[/htb]$ iwconfig
```

![[iwconfig1.png]]

From the above output, it can be observed that the interface has been successfully set to monitor mode. The new name of the interface is now *wlan0mon* instead of wlan0, indicating that it is operating in monitor mode.

## Checking for interfering processes

When putting a card into monitor mode, it will automatically check for interfering processes. It can also be done manually by running the following command:

```shell
Kairos@htb[/htb]$ sudo airmon-ng check
```

![[airmon-ng_check.png]]

As shown in the above output, there are 5 interfering processes that can cause issues by changing channels or putting the interface back into managed mode. If we encounter problems during our engagement, we can terminate these processes using the airmon-ng check kill command.

However, it is important to note that this step should only be taken if we are experiencing challenges during the pentesting process.

```shell
Kairos@htb[/htb]$ sudo airmon-ng check kill
```

![[airmon-neg_kill.png]]

## Starting monitor mode on a specific channel

It is also possible to set the wireless card to a specific channel using `airmon-ng`. We can specify the desired channel while enabling monitor mode on the wlan0 interface.

```shell
Kairos@htb[/htb]$ sudo airmon-ng start wlan0 11
```

![[airmon-ng_channel.png]]

The above command will set the card into monitor mode on channel 11. This ensures that the `wlan0` interface operates specifically on channel 11 while in monitor mode.

## Stopping monitor mode

We can stop the monitor mode on the `wlan0mon` interface using the command `airmon-ng stop wlan0mon`.

```shell
Kairos@htb[/htb]$ sudo airmon-ng stop wlan0mon
```

![[airmon-ng_stop.png]]


We could test to see if our interface is back to managed mode with the iwconfig utility.

```shell
Kairos@htb[/htb]$ iwconfig
```

![[airmon-ng1.png]]

## Moving On

In the next section, we will examine the tool airodump-ng. Airodump-ng is used for packet capture, specifically capturing raw 802.11 frames. It generates several files containing detailed information about all detected access points and clients, allowing us to scan for available WiFi networks effectively.


---

## Questions

1. Activate monitor mode using airmon-ng. How many potentially problematic processes are detected? (Please provide your answer in digit format, e.g., 3)
	Answer: *4*

```bash
sudo airmon-ng start
```

![[airmon-ng_question1.png]]

2. Activate monitor mode using airmon-ng. What is the name of the wireless driver being utilized?
	Answer: *htb80211_chipset*

```bash
sudo airmon-ng start wlan0
```

![[airmon-ng_check_driver.png]]

**Next step:** [[Airodump-ng]]