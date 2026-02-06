Wireless interfaces are a cornerstone of wi-fi penetration testing. After all, our machines transmit and receive this data through these interfaces. If we didn't have them, we could not communicate. We must consider many different aspects when choosing the right interface. If we choose too weak of an interface, we might not be able to capture data during our penetration testing efforts. In this section, we will explore all of the things we should consider when purchasing an interface for wi-fi penetration testing.

---

## How to Choose the Right Interface for the Job

One of the first things that we should consider is capabilities. If our interface is capable of 2.4G and not 5G, we might run into issues when attempting to scan higher band networks. This, of course, is an obvious one, but we should look for the following in our interface:

1. `IEEE 802.11ac or IEEE 802.11ax support`
2. `Supports at least monitor mode and packet injection`

Not all interfaces are equal when it comes to wi-fi penetration testing. We might find that a solo 2.4G card performs better than a more "capable," dual-band card. After all, it comes down to driver support. Not all operating systems have complete support for each card, so we should do our research ahead of time into our chosen chipset.

The chipset of a Wi-Fi card and its driver are crucial factors in penetration testing, as it is important to select a chipset that supports both monitor mode and packet injection. [Airgeddon](https://github.com/v1s1t0r1sh3r3/airgeddon/wiki/Cards%20and%20Chipsets) offers a comprehensive list of Wi-Fi adapters based on their performance. It is important to note that for external Wi-Fi adapters, drivers must be installed manually, whereas built-in adapters in laptops typically do not require manual installation. The installation process for drivers varies depending on the adapter, with different steps required for each model.

## Interface Strength

Much of wi-fi penetration testing comes down to our physical positioning. As such, if a card is too weak, we might find that our efforts will be inadequate. We should always ensure that our card is strong enough to operate at larger and longer ranges. With this, we might want to shoot for longer range cards. One of the ways that we can check on this is through the *iwconfig* utility.

 
```shell
Kairos@htb[/htb]$ iwconfig

wlan0     IEEE 802.11  ESSID:off/any  
          Mode:Managed  Access Point: Not-Associated   Tx-Power=20 dBm   
          Retry short  long limit:2   RTS thr:off   Fragment thr:off
          Power Management:off
```

By default, this is set to the country specified in our operating system. We can check on this with the *iw reg get* command in Linux.


```shell
Kairos@htb[/htb]$ iw reg get

global
country 00: DFS-UNSET
        (2402 - 2472 @ 40), (6, 20), (N/A)
        (2457 - 2482 @ 20), (6, 20), (N/A), AUTO-BW, PASSIVE-SCAN
        (2474 - 2494 @ 20), (6, 20), (N/A), NO-OFDM, PASSIVE-SCAN
        (5170 - 5250 @ 80), (6, 20), (N/A), AUTO-BW, PASSIVE-SCAN
        (5250 - 5330 @ 80), (6, 20), (0 ms), DFS, AUTO-BW, PASSIVE-SCAN
        (5490 - 5730 @ 160), (6, 20), (0 ms), DFS, PASSIVE-SCAN
        (5735 - 5835 @ 80), (6, 20), (N/A), PASSIVE-SCAN
        (57240 - 63720 @ 2160), (N/A, 0), (N/A)
```

With this, we can see all of the different txpower settings that we can do for our region. Most of the time, this might be DFS-UNSET, which is not helpful for us since it limits our cards to `20 dBm`. We can change this of course to our own region, but we should abide by pertinent rules and laws when doing this, as it is against the law in different areas to push our card beyond the maximum set limit, and as well it is not always particularly healthy for our interface.

## Changing the Region Settings for our Interface

Suppose we lived in the United States, we might want to change our interfaces region accordingly. We could do so with the iw reg set command, and simply change the US to our region's two letter code.


```shell
Kairos@htb[/htb]$ sudo iw reg set US
```


Then, we could check this setting again with the iw reg get command.

```shell
Kairos@htb[/htb]$ iw reg get

global
country US: DFS-FCC
        (902 - 904 @ 2), (N/A, 30), (N/A)
        (904 - 920 @ 16), (N/A, 30), (N/A)
        (920 - 928 @ 8), (N/A, 30), (N/A)
        (2400 - 2472 @ 40), (N/A, 30), (N/A)
        (5150 - 5250 @ 80), (N/A, 23), (N/A), AUTO-BW
        (5250 - 5350 @ 80), (N/A, 24), (0 ms), DFS, AUTO-BW
        (5470 - 5730 @ 160), (N/A, 24), (0 ms), DFS
        (5730 - 5850 @ 80), (N/A, 30), (N/A), AUTO-BW
        (5850 - 5895 @ 40), (N/A, 27), (N/A), NO-OUTDOOR, AUTO-BW, PASSIVE-SCAN
        (5925 - 7125 @ 320), (N/A, 12), (N/A), NO-OUTDOOR, PASSIVE-SCAN
        (57240 - 71000 @ 2160), (N/A, 40), (N/A)
```


Afterwards, we can check the txpower of our interface with the `iwconfig` utility.

```shell
Kairos@htb[/htb]$ iwconfig

wlan0     IEEE 802.11  ESSID:off/any  
          Mode:Managed  Access Point: Not-Associated   Tx-Power=20 dBm   
          Retry short  long limit:2   RTS thr:off   Fragment thr:off
          Power Management:off
```


In many cases, our interface will automatically set its power to the maximum in our region. However, sometimes we might need to do this ourselves. First, we would have to bring our interface down.

```shell
Kairos@htb[/htb]$ sudo ifconfig wlan0 down
```


Then, we can set the desired txpower for our interface with the `iwconfig` utility.

```shell
Kairos@htb[/htb]$ sudo iwconfig wlan0 txpower 30
```


After that, we would need to bring our interface back up.

```shell
Kairos@htb[/htb]$ sudo ifconfig wlan0 up
```


Next, we can check the settings again by using the `iwconfig` utility.

```shell
Kairos@htb[/htb]$ iwconfig

wlan0     IEEE 802.11  ESSID:off/any  
          Mode:Managed  Access Point: Not-Associated   Tx-Power=30 dBm   
          Retry short  long limit:2   RTS thr:off   Fragment thr:off
          Power Management:off
```

The default TX power of a wireless interface is typically set to 20 dBm, but it can be increased to 30 dBm using certain methods. However, caution should be exercised, as this adjustment may be illegal in some countries, and users should proceed at their own risk. Additionally, some wireless models may not support these settings, or the wireless chip might technically be capable of transmitting at higher power, but the device manufacturer may not have equipped the device with the necessary heat sink to safely handle the increased output.

The TX power of the wireless interface can be modified using the previously mentioned command. However, in certain instances, this change may not take effect, which could indicate that the kernel has been patched to prevent such modifications.

## Checking Driver Capabilities for our Interface

As mentioned, one of the most important things for our interface, is its capabilities to perform different actions during wi-fi penetration testing. If our interface does not support something, in most cases we simply will not be able to perform that action, unless we acquire another interface. Luckily, we can check on these capabilities via the command line.

The command that we can use to find out this information is the *iw list* command.


```shell
Kairos@htb[/htb]$ iw list

Wiphy phy5
	wiphy index: 5
	max # scan SSIDs: 4
	max scan IEs length: 2186 bytes
	max # sched scan SSIDs: 0
	max # match sets: 0
	max # scan plans: 1
	max scan plan interval: -1
	max scan plan iterations: 0
	Retry short limit: 7
	Retry long limit: 4
	Coverage class: 0 (up to 0m)
	Device supports RSN-IBSS.
	Device supports AP-side u-APSD.
	Device supports T-DLS.
	Supported Ciphers:
			* WEP40 (00-0f-ac:1)
			* WEP104 (00-0f-ac:5)
			<SNIP>
			* GMAC-256 (00-0f-ac:12)
	Available Antennas: TX 0 RX 0
	Supported interface modes:
			 * IBSS
			 * managed
			 * AP
			 * AP/VLAN
			 * monitor
			 * mesh point
			 * P2P-client
			 * P2P-GO
			 * P2P-device
	Band 1:
		<SNIP>
		Frequencies:
				* 2412 MHz [1] (20.0 dBm)
				* 2417 MHz [2] (20.0 dBm)
				<SNIP>
				* 2472 MHz [13] (disabled)
				* 2484 MHz [14] (disabled)
	Band 2:
		<SNIP>
		Frequencies:
				* 5180 MHz [36] (20.0 dBm)
				<SNIP>
				* 5260 MHz [52] (20.0 dBm) (radar detection)
				<SNIP>
				* 5700 MHz [140] (20.0 dBm) (radar detection)
				<SNIP>
				* 5825 MHz [165] (20.0 dBm)
				* 5845 MHz [169] (disabled)
	<SNIP>
		Device supports TX status socket option.
		Device supports HT-IBSS.
		Device supports SAE with AUTHENTICATE command
		Device supports low priority scan.
	<SNIP>
```

Of course, this output can be lengthy, but all the information in here is pertinent to our testing efforts. From the above example, we know that this interface supports the following.

1. `Almost all pertinent regular ciphers`
2. `Both 2.4Ghz and 5Ghz bands`
3. `Mesh networks and IBSS capabilities`
4. `P2P peering`
5. `SAE aka WPA3 authentication`

As such, it can be very important for us to check on our interface's capabilities. Suppose we were testing a WPA3 network, and we came to find out that our interface's driver did not support WPA3, we might be left scratching our head.

## Scanning Available WiFi Networks

To efficiently scan for available WiFi networks, we can use the `iwlist` command along with the specific interface name. Given the potentially extensive output of this command, it is beneficial to filter the results to show only the most relevant information. This can be achieved by piping the output through grep to include only lines containing `Cell`, `Quality`, `ESSID`, or `IEEE`.

```shell
Kairos@htb[/htb]$ iwlist wlan0 scan |  grep 'Cell\|Quality\|ESSID\|IEEE'

          Cell 01 - Address: f0:28:c8:d9:9c:6e
                    Quality=61/70  Signal level=-49 dBm  
                    ESSID:"HTB-Wireless"
                    IE: IEEE 802.11i/WPA2 Version 1
          Cell 02 - Address: 3a:c4:6e:40:09:76
                    Quality=70/70  Signal level=-30 dBm  
                    ESSID:"CyberCorp"
                    IE: IEEE 802.11i/WPA2 Version 1
          Cell 03 - Address: 48:32:c7:a0:aa:6d
                    Quality=70/70  Signal level=-30 dBm  
                    ESSID:"HackTheBox"
                    IE: IEEE 802.11i/WPA2 Version 1
```

From the refined output of the `iwlist` command, we can identify that there are three available WiFi networks. This filtered information focuses on the critical details such as the network cells, signal quality, ESSID, and IEEE specifications, making it straightforward to analyze the available networks.
## Changing Channel & Frequency of Interface

We can use the following command to see all available channels for the wireless interface:

```shell
Kairos@htb[/htb]$ iwlist wlan0 channel

wlan0     32 channels in total; available frequencies :
          Channel 01 : 2.412 GHz
          Channel 02 : 2.417 GHz
          Channel 03 : 2.422 GHz
          Channel 04 : 2.427 GHz
          <SNIP>
          Channel 140 : 5.7 GHz
          Channel 149 : 5.745 GHz
          Channel 153 : 5.765 GHz
```


First, we need to disable the wireless interface which ensures that the interface is not in use and can be safely reconfigured. Then we can set the desired `channel` using the `iwconfig` command and finally, re-enable the wireless interface.

```shell
Kairos@htb[/htb]$ sudo ifconfig wlan0 down
Kairos@htb[/htb]$ sudo iwconfig wlan0 channel 64
Kairos@htb[/htb]$ sudo ifconfig wlan0 up
Kairos@htb[/htb]$ iwlist wlan0 channel

wlan0     32 channels in total; available frequencies :
          Channel 01 : 2.412 GHz
          Channel 02 : 2.417 GHz
          Channel 03 : 2.422 GHz
          Channel 04 : 2.427 GHz
          <SNIP>
          Channel 140 : 5.7 GHz
          Channel 149 : 5.745 GHz
          Channel 153 : 5.765 GHz
          Current Frequency:5.32 GHz (Channel 64)
```


As demonstrated in the above output, `Channel 64` operates at a frequency of `5.32 GHz`. By following these steps, we can effectively change the channel of the wireless interface to optimize performance and reduce interference.


If we prefer to change the frequency directly rather than adjusting the channel, we have the option to do so as well.

```shell
Kairos@htb[/htb]$ iwlist wlan0 frequency | grep Current

          Current Frequency:5.32 GHz (Channel 64)
```


To change the frequency, we first need to disable the wireless interface, which ensures that the interface is not in use and can be safely reconfigured. Then, we can set the desired frequency using the iwconfig command and finally, re-enable the wireless interface.

```shell
Kairos@htb[/htb]$ sudo ifconfig wlan0 down
Kairos@htb[/htb]$ sudo iwconfig wlan0 freq "5.52G"
Kairos@htb[/htb]$ sudo ifconfig wlan0 up
```


We can now verify the current frequency, and this time, we can see that the frequency has been successfully changed to `5.52 GHz`. This change automatically adjusted the channel to the appropriate `channel 104`.

```shell
Kairos@htb[/htb]$ iwlist wlan0 frequency | grep Current

          Current Frequency:5.52 GHz (Channel 104)
```

## Moving On

In this section, we explored how to choose the right interface, change the region settings for our interface, set interface strength, check the drivers, and scan for available WiFi networks. With these foundational steps covered, we are now prepared to delve into the next topic.

In the next section, we will delve into the different modes of a wireless interface. We will examine how each mode operates and its specific applications. Understanding these modes will help us utilize wireless interfaces more effectively for diverse purposes, whether it be for routine connectivity, network troubleshooting, or specialized tasks.



---
## Questions

1. Check the driver capabilities for the interface. How many software interface modes are available? (Answer in digit format: e.g., 3).
	Answer: *2*

```bash
iw list	
```

![[iw_list.png]]

2. Follow the steps shown in the section to scan for available WiFi networks. What is the *ESSID* name of the 3rd WiFi Network (Cell 03)?
	Answer: *HackTheBox-5G*

```bash
iwlist wlan0 scan |  grep "ESSID"
```

![[iwlist_scan.png]]

**Next step:** [[Interface Modes]]