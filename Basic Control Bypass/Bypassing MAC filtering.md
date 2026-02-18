Bypassing MAC filtering in Wi-Fi networks is a technique used to circumvent a basic security measure that many wireless routers implement. MAC filtering involves allowing only devices with specific MAC (Media Access Control) addresses to connect to the network. While this adds a layer of security by restricting access to known devices, it is not foolproof. Skilled individuals can exploit weaknesses in this system to gain unauthorized access. This process typically involves MAC address spoofing, where an attacker changes their device's MAC address to match an allowed device, thereby gaining access to the network.

Suppose we're attempting to connect to a network with MAC filtering enabled. Knowing the password might not be sufficient if our MAC address is not authorized. Fortunately, we can usually overcome this obstacle through MAC spoofing, allowing us to bypass the filtering and gain access to the network.

First, we would want to scout out our network with airodump-ng.

## Scanning Available Wifi Networks

```shell
sudo airodump-ng wlan0mon
```

![[Available_wifi_networks.png]]

From the output, we can see that the ESSID `HTB-Wireless` is available on `channel 1` and has multiple clients connected to it. Suppose we have obtained the credentials for the `HTB-Wireless` WiFi network, with the password `Password123!!!!!!`. Despite having the correct login details, our connection attempts are thwarted by MAC filtering enforced by the network. This security measure restricts access to only authorized devices based on their MAC addresses. As a result, even with the correct password, our device is unable to establish a connection to the network.

To bypass the MAC filtering, we can spoof our MAC address to match one of the connected clients. However, this approach often leads to collision events, as two devices with the same MAC address cannot coexist on the same network simultaneously.

A more effective method would be to either forcefully disconnect the legitimate client through deauthentication attacks, thereby freeing up the MAC address for use, or to wait for the client to disconnect naturally. This strategy is particularly effective in "bring your own device" (BYOD) networks, where devices frequently connect and disconnect.

> [!Note]
> Occasionally, when configuring our MAC address to match that of a client or access point, we may encounter collision events at the data-link layer. This technique of bypassing MAC filtering is most effective when the client we're mimicking is not currently connected to our target network. However, there are instances where these collision events become advantageous to us, serving as a means of denial-of-service (DOS) attack. In the case of a `dual-band` or `multiple access point network`, we may be able to utilize a MAC address of a client connected to a separate access point within the same wireless infrastructure.

We can also check if there is a 5 GHz band available for the ESSID. If the 5 GHz band is available, we can attempt to connect to the network using that frequency, which would avoid collision events since most clients are connected to the 2.4 GHz band.

## Scanning Networks Running on 5Ghz Band

```shell
sudo airodump-ng wlan0mon --band a
```

![[scanning_5gh_Networks.png]]

From the above output, we can confirm that the ESSID `HTB-Wireless-5G` with the same BSSID is also operating on the `5 GHz` band. Since no clients are currently connected to the 5 GHz band, we can spoof our MAC address using tools such as [macchanger](https://github.com/alobbs/macchanger) to match one of the clients connected to the 2.4 GHz band and connect to the 5 GHz network without any collision events.

Before changing our MAC address, let's stop the monitor mode on our wireless interface.

```shell
sudo airmon-ng stop wlan0mon
```

![[bypass_mac_1.png]]

Let's check our current MAC address before changing it. We can do this by running the following command in the terminal.

```shell
sudo macchanger wlan0
```

![[check_mac.png]]

As shown in the output, our Current MAC address and Permanent MAC address are `00:c0:ca:98:3e:e0`. Let's use `macchanger` to change our MAC address to match one of the clients connected to the 2.4 GHz network, specifically `3E:48:72:B7:62:2A`. This process involves disabling the `wlan0` interface, executing the `macchanger` command to adjust the MAC address, and finally reactivating the `wlan0` interface. Following these steps will effectively synchronize our device's MAC address with the specified client's address on the 2.4 GHz network.

### Disable wlan0 interface

```shell
sudo ifconfig wlan0 down
```

### Change the MAC address

```shell
sudo macchanger wlan0 -m 3E:48:72:B7:62:2A
```

![[macchanger.png]]

### Enable wlan0 interface

```shell
sudo ifconfig wlan0 up
```

After bringing the wlan0 interface back up, we can utilize the `ifconfig` command to confirm that our MAC address has indeed been modified. This step ensures that our device now adopts the new MAC address we specified earlier, aligning with the desired client's MAC address connected to the 2.4 GHz network.

```shell
ifconfig wlan0
```

![[ifconfig-1.png]]

Now that our MAC address has been changed to match one of the clients connected to the 2.4 GHz network, we can proceed to connect to the 5 GHz WiFi network named `HTB-Wireless-5G`. This can be done either through the graphical user interface (GUI) of the system's network manager or via the command line using tools like NetworkManager's command-line interface (nmcli).

![[bypass_mac_2-1.png]]

![[bypass_mac_3-1.png]]

After successfully connecting to the 5 GHz network, we can verify the connection status by running the `ifconfig` command once more. This time, we should observe that a DHCP-assigned IP address has been allocated by the WiFi network.

```shell
ifconfig
```

![[ifconfig1-1.png]]

Once connected to the WiFi network, we can scan for other clients connected to the same network within the IP range.

## Closing Thoughts

Wi-Fi penetration testing is a critical skill for assessing and improving network security. The basics we’ve covered in this module provide a strong starting point. We explored how to connect to Wi-Fi networks using both GUI and CLI, use the aircrack-ng suite to perform different attacks, and discover hidden SSIDs. Additionally, we discussed changing interface modes, adjusting signal strength and frequencies, and bypassing MAC filters to overcome access restrictions. These tools and techniques offer a solid introduction to wireless security, setting the stage for deeper exploration and advanced skills.

Mastering these fundamentals will empower you to not only identify vulnerabilities but also to take proactive steps in securing Wi-Fi networks. As you continue exploring, remember that each network presents unique challenges, and honing your skills through practice is the best way to stay ahead in this dynamic field. Keep pushing the boundaries, and soon, more advanced techniques will become second nature.

## Questions

1. What is the ESSID of the WiFi network operating on the 5 GHz band?

	Answer: *CyberNet-Secure-5G*

```shell
sudo airmon-ng start wlan0
sudo airdump-ng wlan0mon --band a
```

![[question1.png]]

2. Execute the MAC Filtering bypass as demonstrated in the section to establish a connection to the 5 GHz band. Once connected, locate the flag at IP address 192.168.2.1.

	Answer: *HTB{bfcc811c7b9b4c7cf63c5c2e968e13e0}*

- Stop connection.

```shell
sudo airmon-ng stop wlan0mon
```

![[stop_wlan0mon.png]]

- Change MAC.

```shell
sudo macchanger wlan0 -m 1e:7f:51:2b:de:23
```

![[mac_chenged.png]]

- Raise the wlan0.

```shell
sudo ifconfig wlan0 up
```

- Check the new MAC.

```shell
ifconfig wlan0
```

![[check_mac1.png]]

- Check IP assigned.

```shell
ifconfig
```

![[check_IP.png]]

- Visit http://192.168.2.1 on a browser.

![[flag.png]]

**Next lesson:** [[Scenario]]