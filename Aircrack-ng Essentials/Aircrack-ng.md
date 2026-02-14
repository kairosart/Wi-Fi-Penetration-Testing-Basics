Aircrack-ng is a powerful tool designed for network security testing, capable of cracking WEP and WPA/WPA2 networks that use pre-shared keys or PMKID. Aircrack-ng is an offline attack tool, as it works with captured packets and *doesn't need direct interaction with any Wi-Fi device*.

## Aircrack-ng Benchmark

Prior to commencing passphrase cracking with Aircrack-ng, it is imperative to assess the benchmark of the host system to ensure its capability to execute brute-force attacks effectively. Aircrack-ng has a benchmark mode to test CPU performance. We'll start with benchmarking to evaluate the performance capabilities of our cracking system.

```shell
aircrack-ng -S
```

![[Aircrack-ng_Benchmark..png]]

The above output estimates that our CPU can crack approximately 1,628.101 passphrases per second. Since `Aircrack-ng` fully utilizes the CPU, the cracking speed can decrease significantly if other demanding tasks are running on the system simultaneously.

## Cracking WEP

Aircrack-ng is capable of recovering the WEP key once a sufficient number of encrypted packets have been captured using Airodump-ng. It is possible to save only the captured IVs (Initialization Vectors) using the `--ivs` option in Airodump-ng. Once enough IVs are captured, we can utilize the `-K` option in Aircrack-ng, which invokes the Korek WEP cracking method to crack the WEP key.

```shell
aircrack-ng -K HTB.ivs 
```

![[cracking_WEP.png]]

## Cracking WPA

Aircrack-ng has the capability to crack the WPA key once a "four-way handshake" has been captured using Airodump-ng. To crack WPA/WPA2 pre-shared keys, only a dictionary-based method can be employed, which necessitates the use of a wordlist containing potential passwords. A "four-way handshake" serves as the required input. For WPA handshakes, a complete handshake comprises four packets. However, *Aircrack-ng can effectively operate with just two packets. Specifically, EAPOL packets 2 and 3, or packets 3 and 4, are considered a full handshake*.

```shell
aircrack-ng HTB.pcap -w /opt/wordlist.txt
```

![[cracking_WPA.png]]
## Moving On

In the next section, we will examine how to connect to different types of Wi-Fi networks using both graphical and command-line interfaces, once we have recovered the network password.

## Questions

1. Utilize Aircrack-ng to crack the WEP key from the file located at "*/opt/WEP.ivs*" and submit the found key as the answer.

	Answer: *AE:5B:7F:3A:03:D0:AF:9B:F6:8D:A5:E2:C7*

```shell
sudo aircrack-ng -K /opt/WEP.ivs
```

![[cracking_WEP1.png]]


2. Utilize Aircrack-ng to crack the WPA key for the ESSID "*Coherer*" from the file located at "*/opt/WPA_Capture.pcap*" and submit the found key as the answer.

	Answer: *Induction*

```shell
aircrack-ng /opt/WPA_Capture.pcap -w /opt/wordlist.txt
```

Choose the *Coherer* ESSID.

![[cracking_WPA1.png]]

