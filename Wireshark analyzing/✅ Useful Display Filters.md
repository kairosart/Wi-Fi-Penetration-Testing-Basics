Type these in the top filter bar:

## Show only Beacon frames

```wireshark
wlan.fc.type_subtype == 0x08
```

## Show only Probe Requests

```wireshark
wlan.fc.type_subtype == 0x04
```

## Show only Deauthentication frames

```wireshark
wlan.fc.type_subtype == 0x0c
```

## Show traffic from a specific BSSID

```wireshark
wlan.bssid == AA:BB:CC:DD:EE:FF
```

Replace with your AP MAC.

