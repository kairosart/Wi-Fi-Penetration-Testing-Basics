[Airdecap-ng](https://www.aircrack-ng.org/doku.php?id=airdecap-ng) is a valuable tool for decrypting wireless capture files once we have obtained the `key` to a network. It can decrypt `WEP`, `WPA PSK`, and `WPA2 PSK` captures. Additionally, it can remove wireless headers from an `unencrypted` capture file. This tool is particularly useful in analyzing the data within captured packets by making the content readable and removing unnecessary wireless protocol information.

Airdecap-ng can be used for the following:

- Removing wireless headers from an open network capture (Unencrypted capture).
- Decrypting a WEP-encrypted capture file using a hexadecimal WEP key.
- Decrypting a WPA/WPA2-encrypted capture file using the passphrase.

## Using Airdecap-ng

```shell
airdecap-ng [options] <pcap file>
```

|**Option**|**Description**|
|---|---|
|`-l`|don't remove the 802.11 header|
|`-b`|access point MAC address filter|
|`-k`|WPA/WPA2 Pairwise Master Key in hex|
|`-e`|target network ascii identifier|
|`-p`|target network WPA/WPA2 passphrase|
|`-w`|target network WEP key in hexadecimal|

`Airdecap-ng` generates a new file with the suffix `-dec.cap,` which contains the decrypted or stripped version of the original input file. For instance, an input file named `HTB-01.cap` will result in an unencrypted output file named `HTB-01-dec.cap`.

In the encrypted capture file created using `airodump-ng` and opened using Wireshark as shown below, the `Protocol` tab only displays `802.11` without specifying the actual protocol of the message. Similarly, the `Info` tab does not provide meaningful information. Additionally, the `source` and `destination`fields only contain MAC addresses instead of the corresponding IP addresses.

![[Wireshark_1.jpeg]]

Conversely, in the decrypted capture file using `airdecap-ng`, observe how the `Protocol` tab displays the correct protocol, such as ARP, TCP, DHCP, HTTP, etc. Additionally, notice how the `Info` tab provides more detailed information, and it correctly displays the `source` and `destination` IP addresses.

![[Wireshark_2.jpeg]]

## Removing Wireless Headers from Unencrypted Capture file

Capturing packets on an open network would result in an `unencrypted` capture file. Even if the capture file is already unencrypted, it may still contain numerous frames that are not relevant to our analysis. To streamline the data, we can utilize `airdecap-ng` to eliminate the wireless headers from an unencrypted capture file.

To remove the wireless headers from the capture file using Airdecap-ng, we can use the following command:

```shell
airdecap-ng -b <bssid> <capture-file>
```

Replace with the MAC address of the access point and with the name of the capture file.

```shell
sudo airdecap-ng -b 00:14:6C:7A:41:81 opencapture.cap
```

![[Remove_wireless_headers.png]]

This will produce a decrypted file with the suffix `-dec.cap`, such as `opencapture-dec.cap`, containing the streamlined data ready for further analysis.

## Decrypting WEP-encrypted captures

Airdecap-ng is a powerful tool for decrypting WEP-encrypted capture files. Once we have obtained the hexadecimal `WEP key`, we can use it to decrypt the captured packets. This process will remove the wireless encryption, allowing us to analyze the data.

To decrypt a WEP-encrypted capture file using Airdecap-ng, we can use the following command:

```shell
airdecap-ng -w <WEP-key> <capture-file>
```

Replace *WEP-key* with the hexadecimal WEP key and with the name of the capture file.

For example:

```shell
sudo airdecap-ng -w 1234567890ABCDEF HTB-01.cap
```

![[Decrypt_wep.png]]

This will produce a decrypted file with the suffix `-dec.cap`, such as `HTB-01-dec.cap`, containing the unencrypted data ready for further analysis.

## Decrypting WPA-encrypted captures

Airdecap-ng can also decrypt WPA-encrypted capture files, provided we have the `passphrase`. This tool will strip the WPA encryption, making it possible to analyze the captured data.

To decrypt a WPA-encrypted capture file using Airdecap-ng, we can use the following command:

```shell
airdecap-ng -p <passphrase> <capture-file> -e <essid>
```

Replace `passphrase` with the *WPA passphrase*,  `capture-file` with the name of the *capture file* and `essid` with the *ESSID name* of the respective network.

For example:

```shell
sudo airdecap-ng -p 'abdefg' HTB-01.cap -e "Wireless Lab"
```

![[Decrypt_wpa.png]]

This will produce a decrypted file with the suffix `-dec.cap`, such as `HTB-01-dec.cap`, containing the unencrypted data ready for further analysis.

## Moving On

In this section, we explored how to decrypt captured packet files to obtain essential data from network traffic. We examined the process of using tools to remove encryption from packets and access valuable information for security analysis.

In the next section, we will delve into aircrack-ng, a powerful program designed for cracking 802.11 WEP and WPA/WPA2-PSK keys. Aircrack-ng is a crucial tool for network security professionals, used to perform brute-force attacks to recover encryption keys and assess the strength of wireless network security measures.

## Questions

1. Decrypt the file located at */opt/decrypt.cap* using *airdecap-ng*. Look for sensitive data indicating a user is attempting to log in to a website with a POST request. What is the username associated with this login attempt? (The WPA key for ESSID named *CyberNet-Secure* is *Password123!!!!!!*)

	Answer: *htb-admin*
- Run.
```shell
sudo airdecap-ng -p 'Password123!!!!!!' decrypt.cap -e "CyberNet-Secure"
```

- Open the file */opt/decrypt-dec.cap* with Wireshark.
- Look for POST requests.

![[Wireshark_3.jpeg]]


2. Decrypt the file located at */opt/decrypt.cap* using *airdecap-ng*. Look for sensitive data indicating a user is attempting to log in to a website with a POST request. What is the password entered during this login attempt? (The WPA key for ESSID named *CyberNet-Secure* is *Password123!!!!!!*)

	Answer: *HTB_@cademy_ROCKS*

**Next lesson:** [[Aircrack-ng]]