# Pairing and Key Checks

The paring and key checks are based upon the rules set by Themis [Secure Session](https://docs.cossacklabs.com/themis/crypto-theory/cryptosystems/secure-session/)

### Key Check - Packet ID 4

This should be done after Packet ID 0. Or the Hello Packet. To give context for the other connection

Secure Session works by Device A sending a random string encrypted with Device B's public key.  Then Device B will decrypt the message and send it back encrypted with Device A's Public Key.  

If the device A gets the same message pack the "session" is able to be resumed



### Device Pairing

#### Pair Request

The first step in pairing two devices together is to see device B wants to pair with you. This is done by first opening a TCP connection and sending a packet 2

```json5
{
    protocol: 0,
    packet: 2,
    content: {
        device_id,
        device_name,
    }
}
```

If device B wants to connect it will send a packet 2 with its own device_id and device_name. If they do not want to connect they will end the connection



#### Pairing

Give the Secure Session part of themis a read through before proceeding 




