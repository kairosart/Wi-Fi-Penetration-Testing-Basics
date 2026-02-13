The primary function of [Aireplay-ng](https://www.aircrack-ng.org/doku.php?id=aireplay-ng) is to generate traffic for later use in aircrack-ng for cracking the WEP and WPA-PSK keys. There are different attacks that can cause deauthentication for the purpose of capturing WPA handshake data, fake authentications, Interactive packet replay, hand-crafted ARP request injection, and ARP-request reinjection. With the packetforge-ng tool it's possible to create arbitrary frames.

To list all the features of `aireplay-ng` we use the following command.

```shell
aireplay-ng
```

![[aireplay-ng.png]]

It currently implements multiple different attacks:

| **Attack** | **Attack Name**                      |
| ---------- | ------------------------------------ |
| *Attack 0*   | Deauthentication                     |
| *Attack 1*   | Fake authentication                  |
| *Attack 2*   | Interactive packet replay            |
| *Attack 3*   | ARP request replay attack            |
| *Attack 4*   | KoreK chopchop attack                |
| *Attack 5*   | Fragmentation attack                 |
| *Attack 6*   | Cafe-latte attack                    |
| *Attack 7*   | Client-oriented fragmentation attack |
| *Attack 8*   | WPA Migration Mode                   |
| *Attack 9*   | Injection test                       |

As we can see, the flag for deauthentication is `-0` or `--deauth`. For this module, we will focus on the `deauthentication` attack. This attack can be used to disconnect clients from the access point (AP). By using `aireplay-ng`, we can send `deauthentication` packets to the AP. The AP will mistakenly believe that these deauthentication requests are coming from the clients themselves, when in fact, we are the ones sending them.

Testing for Packet Injection

Before sending deauthentication frames, it's important to verify if our wireless card can successfully inject frames into the target access point (AP). This can be tested by measuring the ping response times from the AP, which gives us an indication of the link quality based on the percentage of responses received. Furthermore, if we are using two wireless cards, this test can help identify which card is more effective for injection attacks.

Let's enable monitor mode and set the channel for the interface to 1. We can do this using the airmon-ng command `airmon-ng start wlan0 1`. Alternatively, we can use the `iw` command to set the channel as follows:

```shell
sudo iw dev wlan0mon set channel 1
```

Once we have our interface in monitor mode, it is very easy for us to test it for packet injection. We can utilize Aireplay-ng's test mode as follows.

```shell
sudo aireplay-ng --test wlan0mon
```

![[aireplay-ng_test.png]]

If everything is in order, we should see the message Injection is working! This indicates that our interface supports packet injection, and we are ready to use aireplay-ng to perform a deauthentication attack.

## Using Aireplay-ng to perform Deauthentication

First, let's use airodump-ng to view the available WiFi networks, also known as access points (APs).

```shell
sudo airodump-ng wlan0mon
```

![[Deauthentication.png]]

From the above output, we can see that there are three available WiFi networks, and two clients are connected to the network named HTB. Let's send a deauthentication request to one of the clients with the station ID *00:0F:B5:32:31:31*.

```shell
sudo aireplay-ng -0 5 -a 00:14:6C:7A:41:81 -c 00:0F:B5:32:31:31 wlan0mon
```

![[Clients_connected.png]]

- `-0` means deauthentication
- `5` is the number of deauths to send (you can send multiple if you wish); `0` means send them continuously
- `-a 00:14:6C:7A:41:81` is the MAC address of the access point
- `-c 00:0F:B5:32:31:31` is the MAC address of the client to deauthenticate; if this is omitted then all clients are deauthenticated
- `wlan0mon` is the interface name

Once the clients are deauthenticated from the AP, we can continue observing `airodump-ng` to see when they reconnect.

```shell
sudo airodump-ng wlan0mon
```

![[Clients_reconnected.png]]

In the output above, we can see that after sending the deauthentication packet, the client disconnects and then reconnects. This is evidenced by the increase in `Lost` packets and `Frames` count.

Additionally, a `four-way handshake` would be captured by `airodump-ng`, as shown in the output. By using the `-w` option in airodump-ng, we can save the captured WPA handshake into a `.pcap` file. This file can then be used with tools like `aircrack-ng` to crack the pre-shared key (PSK). We will cover aircrack-ng and the process of cracking PSKs in the upcoming aircrack-ng section.

> [!Note]
> In the lab environment, the clients continuously reconnect to the AP every few seconds. Therefore, it is possible to capture the `WPA handshake` without sending deauthentication requests.

## Moving On

In the next section, we will take an in-depth look at the `airdecap-ng` tool. Airdecap-ng is a powerful utility used to decrypt captured packet files that are encrypted with WEP, WPA, or WPA2 protocols. By utilizing this tool, we can effectively decode encrypted network traffic and gain access to the data contained within, which is crucial for performing security assessments and analyzing network vulnerabilities.

We will explore how to use airdecap-ng to decrypt captured packets, discuss the various options available for decryption, and examine practical scenarios where decrypting network traffic can be beneficial for network security analysis.

