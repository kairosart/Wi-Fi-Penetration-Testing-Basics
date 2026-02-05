There are two primary authentication systems commonly used in WiFi networks: `Open System Authentication` and `Shared Key Authentication`.

![[auth_methods_.png]]

- `Open System Authentication` is straightforward and does not require any shared secret or credentials for initial access. This type of authentication is typically used in open networks where no password is needed, allowing any device to connect to the network without prior verification.
    
- `Shared Key Authentication`, as the name suggests, involves the use of a shared key. In this system, both the client and the access point verify each other's identities by computing a challenge-response mechanism based on the shared key.
    

While many other methods exist, especially in `Enterprise` environments or with advanced protocols like `WPA3` and `Enhanced Open`, these two are the most prevalent.

## Open System Authentication

As the name implies, open system authentication does not require any shared secret or credentials right away. This authentication type is commonly found for open networks that do not require a password. For Open System Authentication, it tends to follow this order:

1. The client (station) sends an authentication request to the access point to begin the authentication process.
2. The access point then sends the client back an authentication response, which indicates whether the authentication was accepted.
3. The client then sends the access point an association request.
4. The access point then responds with an association response to indicate whether the client can stay connected.

![[Open_system.png]]

As shown in the image above, Open System Authentication does not require any credentials or authentication. Devices can connect directly to the network without needing to enter a password, making it convenient for public or guest networks where ease of access is a priority.

While Open System Authentication is convenient for public or guest networks, Shared Key Authentication offers an additional layer of security by ensuring that only devices with the correct key can access the network.

## Shared Key Authentication

On the other hand shared key authentication does involve a shared key, as the name implies. In this authentication system, the client and access point prove their identities through the computation of a challenge. This method is often associated with Wired Equivalent Privacy (`WEP`) and Wi-Fi Protected Access (`WPA`). It provides a basic level of security through the use of a pre-shared key.

![[Shared_auth.png]]

### Authentication with WEP

1. `Authentication request:` Initially, as it goes, the client sends the access point an authentication request.
2. `Challenge:` The access point then responds with a custom authentication response which includes challenge text for the client.
3. `Challenge Response:` The client then responds with the encrypted challenge, which is encrypted with the WEP key.
4. `Verification:` The AP then decrypts this challenge and sends back either an indication of success or failure.

![[Wep_process.png]]
### Authentication with WPA

On the flip side, WPA utilizes a form of authentication that includes a four-way handshake. Commonly, this replaces the association process with more verbose verification, and in the case of WPA3, the authentication portion is even crazier for the pairwise key generation. From a high level, this is performed like the following.

1. `Authentication Request:` The client sends an authentication request to the AP to initiate the authentication process.
2. `Authentication Response:` The AP responds with an authentication response, which indicates that it is ready to proceed with authentication.
3. `Pairwise Key Generation:` The client and the AP then calculate the PMK from the PSK (password).
4. `Four-Way Handshake:` The client and access point then undergo each step of the four way handshake, which involves nonce exchange, derivation, among other actions to verify that the client and AP truly know the PSK.
 
![[Wpa_process.png]]

Shared key authentication type also involves [WPA3](https://documentation.meraki.com/MR/Wi-Fi_Basics_and_Best_Practices/WPA3_Encryption_and_Configuration_Guide), the latest and most secure WiFi security standard. WPA3 introduces significant improvements over its predecessors, including more robust encryption and enhanced protection against brute force attacks. One of its key features is `Simultaneous Authentication of Equals (SAE)`, which replaces the `Pre-Shared Key (PSK)` method used in WPA2, providing better protection for passwords and individual data sessions.

Despite its advantages, WPA3 adoption has been slower due to hardware restrictions. Many existing devices do not support WPA3 and require firmware updates or replacements to be compatible. This creates a barrier to widespread implementation, particularly in environments with a large number of legacy devices. Consequently, while WPA3 offers superior security, its use is not yet widespread, and many networks continue to rely on older standards like WPA2 until the necessary hardware upgrades become more accessible and affordable.

## Moving On

In this section, we discussed various authentication types. We explored their unique characteristics, security features, and the evolution of these protocols to address emerging threats.

In the next section, we will focus on WiFi interfaces. We will cover several important aspects, including how to:

- Adjust signal strength
- Change frequency and channel settings
- Modify region settings
- Check driver capabilities
- Scan for available WiFi networks

Understanding these elements will enable us to optimize and troubleshoot our wireless connections more effectively. This comprehensive approach will ensure that our networks operate smoothly and efficiently, meeting our specific needs and adapting to different environments.

**Next step:** [[Wi-Fi Interfaces]]