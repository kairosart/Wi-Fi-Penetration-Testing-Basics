Bypassing MAC filtering in Wi-Fi networks is a technique used to circumvent a basic security measure that many wireless routers implement. MAC filtering involves allowing only devices with specific MAC (Media Access Control) addresses to connect to the network. While this adds a layer of security by restricting access to known devices, it is not foolproof. Skilled individuals can exploit weaknesses in this system to gain unauthorized access. This process typically involves MAC address spoofing, where an attacker changes their device's MAC address to match an allowed device, thereby gaining access to the network.

Suppose we're attempting to connect to a network with MAC filtering enabled. Knowing the password might not be sufficient if our MAC address is not authorized. Fortunately, we can usually overcome this obstacle through MAC spoofing, allowing us to bypass the filtering and gain access to the network.

First, we would want to scout out our network with airodump-ng.

## Scanning Available Wifi Networks

