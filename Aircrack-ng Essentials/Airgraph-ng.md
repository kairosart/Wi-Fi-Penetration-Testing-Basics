`Airgraph-ng` is a Python script designed for generating graphical representations of wireless networks using the CSV files produced by `Airodump-ng`. These CSV files from Airodump-ng capture essential data regarding the associations between wireless clients and Access Points (APs), as well as the inventory of probed networks. Airgraph-ng processes these CSV files to produce two distinct types of graphs:

- Clients to AP Relationship Graph: This graph illustrates the connections between wireless clients and Access Points, providing insights into the network topology and the interactions between devices.
- Clients Probe Graph: This graph showcases the probed networks by wireless clients, offering a visual depiction of the networks scanned and potentially accessed by these devices.

By leveraging Airgraph-ng, users can visualize and analyze the relationships and interactions within wireless networks, aiding in network troubleshooting, optimization, and security assessment.

## Clients to AP Relationship Graph

The Clients to AP Relationship (CAPR) graph illustrates the connections between clients and access points (APs). Since this graph emphasizes clients, it will not display any APs without connected clients.

The access points are color-coded based on their encryption type:

- Green for WPA
- Yellow for WEP
- Red for open networks
- Black for unknown encryption.

```shell
sudo airgraph-ng -i HTB-01.csv -g CAPR -o HTB_CAPR.png
```

![[HTB_CAPR.png]]

> [!Note]
> The `HTB-01.csv` file can be obtained using the `airodump-ng -w HTB` command as shown in previous section.

## Common Probe Graph

The Common Probe Graph (CPG) in Airgraph-ng visualizes the relationships between wireless clients and the access points (APs) they probe for. It shows which APs each client is trying to connect to by displaying the probes sent out by the clients. This graph helps identify which clients are probing for which networks, even if they are not currently connected to any AP.

```shell
sudo airgraph-ng -i HTB-01.csv -g CPG -o HTB_CPG.png
```

![[HTB_CPG.png|796x294]]

## Moving On

By leveraging `Airgraph-ng`, attackers can visualize and analyze the relationships and interactions within wireless networks. This powerful tool provides graphical representations of network connections, enabling a comprehensive understanding of the network's structure. Such insights are invaluable for conducting thorough security assessments and strategically mapping out attack chains. By identifying key nodes and potential vulnerabilities, Airgraph-ng aids in developing more effective strategies for network penetration and defense.

In the next section, we will explore the `aireplay-ng` tool. Aireplay-ng is used to generate traffic, making it an essential tool for testing and analyzing wireless networks.

## Questions

1. Use airgraph-ng on the file /opt/data.csv to create a graph of Clients to AP Relationship (CAPR). How many total clients are shown in the generated graphic? (Answer in digit format: e.g., 3)

	Answer: *9*

```shell
sudo airgraph-ng -i HTB-01.csv -g CAPR -o HTB_CAPR1.png
```

![[HTB_CAPR1.png|1159x911]]

2.  Use airgraph-ng on the file /opt/data.csv to create a graph of Clients to AP Relationship (CAPR). How many clients are connected to the AP 'CyberNet-Secure'? (Answer in digit format: e.g., 3)

	Answer: *6*



```shell
sudo airgraph-ng -i /opt/data.csv -g CPG -o HTB_CPG1.png
```

![[HTB_CPG1.png|1176x962]]

3.  Use airgraph-ng on the file /opt/data.csv to create a Common Probe graph (CPG). How many clients are probing for the AP 'HTB-Wireless'? (Answer in digit format: e.g., 3)
	Answer: *2*

**Next step:** [[Aireplay-ng]]