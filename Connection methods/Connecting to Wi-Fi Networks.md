Connecting to Wi-Fi networks using Linux involves a few straightforward steps. First, we need to scan for available networks, which can be done using tools like `iwlist` or through a `graphical network manager`. Once we identify the target network, we can connect by configuring the appropriate settings. In this section, we will explore how to connect to Wi-Fi networks using both graphical and command-line interfaces.

---

## Using GUI

Connecting to a Wi-Fi network with a GUI is typically a straightforward process. Once we obtain the valid credentials (either a passphrase for WPA/WPA2-Personal, username/password for WPA/WPA2-Enterprise or key for WEP), we simply input them into the password prompt provided by the system's network manager.

Here’s a breakdown of how this process usually works using GUI:

1. `Scan for Networks`
2. `Select the Network`
3. `Enter Credentials`
4. `Connect`
 
![[wifi_connect.png]]![[bypass_mac_2.png]]
![[bypass_mac_3.png]]

## Using CLI

If we've obtained the correct password for a network or simply want to connect to one, we may not always have access to the graphical network manager. In such cases, we’ll need to connect to the wireless network using the terminal. Fortunately, there are several methods available to achieve this from the command line. To connect to a network via the command line, we would use `wpa_supplicant` along with a `configuration` file that contains the necessary network details. This allows us to authenticate and connect to the network directly from the terminal.

Typically, we would switch our interface to monitor mode to scan for nearby networks. However, if we're limited or our interface doesn't support monitor mode, we can use managed mode instead. In this case, we can utilize the iwlist tool along with some grep parameters to filter and display useful information like the cell, signal quality, ESSID, and IEEE version of the networks around us.

```shell
sudo iwlist wlan0 s | grep 'Cell\|Quality\|ESSID\|IEEE'
```

![[iwlist.png]]

As shown in the output above, there are three available Wi-Fi networks. One uses `WEP`, one uses `WPA`, and one uses `WPA-Enterprise`. We'll begin by connecting to the WEP network first.

## Connecting to WEP Networks

If the target network is using WEP, connecting is straightforward. We just need to provide the `SSID`, the `WEP hex key`, and set the WEP key index using `wep_tx_keyidx` in a configuration file (e.g., wep.conf) to establish the connection. Additionally, we set `key_mgmt=NONE`, which is used for WEP or networks with no security.

```config
network={
	ssid="HackTheBox"
    key_mgmt=NONE
    wep_key0=3C1C3A3BAB
    wep_tx_keyidx=0
}
```

Once the configuration file is ready, we can use `wpa_supplicant` to connect to the network. We run the command with the `-c` option to specify the configuration file and the `-i` option to specify the network interface.

```shell
sudo wpa_supplicant -c wep.conf -i wlan0
```

![[wpa_supplicant.png]]

After connecting, we can obtain an IP address by using the `dhclient` utility. This will assign an IP from the network's DHCP server, completing the connection setup.

```shell
sudo dhclient wlan0
ifconfig wlan0
```


![[ifconfig.png]]

## Connecting to WPA Personal Networks

If the target network uses WPA/WPA2, we'll need to create a wpa_supplicant configuration file (eg: wpa.conf) with the correct `PSK` (Pre-Shared Key) and `SSID`. This file will look like the following:

```config
network={
	ssid="HackMe"
    psk="password123"
}
```

Then we could initiate our wpa connection to the AP using the following command.

```shell
sudo wpa_supplicant -c wpa.conf -i wlan0
```

![[wpa_supplicant1.png]]

After connecting, we can obtain an IP address by using the `dhclient` utility. This will assign an IP from the network's DHCP server, completing the connection setup. However, if we have a previously assigned DHCP IP address from a different connection, we'll need to release it first. Run the following command to remove the existing IP address:

```shell
sudo dhclient wlan0 -r
```

![[dhclient_kill.png]]

We can now run the dhclient command. This will assign an IP from the network's DHCP server, completing the connection setup.

```shell
sudo dhclient wlan0 
```

```shell
ifconfig wlan0
```

![[ifconfig1.png]]

If the network uses `WPA3` instead of WPA2, we would need to add `key_mgmt=SAE` to our wpa_supplicant configuration file to connect to it. This setting specifies the use of the `Simultaneous Authentication of Equals (SAE)` protocol, which is a key component of WPA3 security.

## Connecting to WPA Enterprise

If the target network uses WPA/WPA2 Enterprise (MGT), we'll need to create a wpa_supplicant configuration file with the correct `identity`, `password`, `SSID` and `key_mgmt`. This file will look like this:

```config
network={
  ssid="HTB-Corp"
  key_mgmt=WPA-EAP
  identity="HTB\Administrator"
  password="Admin@123"
}
```

Once the configuration file is ready, we can use `wpa_supplicant` to connect to the network. We run the command with the `-c` option to specify the configuration file and the `-i` option to specify the network interface.

```shell
sudo wpa_supplicant -c wpa_enterprsie.conf -i wlan0
```

![[wpa_enterprsie.png]]

After connecting, we can obtain an IP address by using the `dhclient` utility. This will assign an IP from the network's DHCP server, completing the connection setup. However, if we have a previously assigned DHCP IP address from a different connection, we'll need to release it first. Run the following command to remove the existing IP address:

```shell
sudo dhclient wlan0 -r
```

![[dhclient_kill-1.png]]

```shell
sudo dhclient wlan0
ifconfig wlan0
```

![[ifconfig2.png]]


## Connecting with Network Manager Utilities

One of the ways that we can easily connect to wireless networks in Linux is through the usage of nmtui. This utility will give us a somewhat graphical perspective while connecting to these wireless networks.

```shell
sudo nmtui
```

Once we enter the command above, we should see the following view.

![[nmtui.png]]

If we select `Activate a connection`, we should be able to choose from a list of wireless networks. We might be prompted to enter our password upon connecting to the network.

![[2-connect.png|606x474]]
## Moving On

In the next section, we will delve into effective methods for discovering `hidden SSIDs` (Service Set Identifiers). Hidden SSIDs are networks that do not broadcast their network name, making them less visible to casual users and potential attackers. However, with the right techniques and tools, these hidden networks can still be identified and analyzed.

## Questions

1. Connect to the WPA Wi-Fi network named "*CyberNet-Secure*" with the PSK "Password123!!!!!!". Once connected, locate the flag at the IP address `192.168.1.1`.

	Answer: *HTB{C0NN3cTeD_t0_WPA}*

 - After connecting run the following on the terminal.
```shell
curl http://192.168.1.1
```


2.  Connect to the WEP Wi-Fi network named "*HackTheBox-WEP*" using the key "1A2B3C4D5E". Once connected, locate the flag at the IP address `192.168.2.1`.

	Answer: *HTB{W3p_!s_EasY}*

- Use the same method as the previous question.

3. Connect to the WPA-Enterprise Wi-Fi network named "*HTB-Corp*" with username "*HTB\Sentinal*" and password "sentinal". Once connected, locate the flag at the IP address `192.168.3.1`.

	Answer: *HTB{ENT3RPR!SE_C00n3ctED*

- Create a file called *wpa_enterprise.conf* .
- Add the following configuration and save it.

```text
network={
  ssid="HTB-Corp"
  key_mgmt=WPA-EAP
  identity="HTB\Sentinal"
  password="sentinal"
}
```

- Run.

```shell
sudo dhclient wlan0 -r
sudo dhclient wlan0
ifconfig
```

![[ifconfig3.png]]

You can see you're on the 192.168.3.X network.

- Run.

```shell
curl http://192.168.3.1
```

**Next lesson:** [[Finding Hideen SSIDs]]