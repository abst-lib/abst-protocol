# Pairing and Key Checks

### Key Check - Packet ID 5 and 6

This should be done after Packet ID 2. Or the Hello Packet. To give context for the other connection

Secure Session works by Device A sending a random string encrypted with Device B's public key.  Then Device B will decrypt the message and send it back encrypted with Device A's Public Key.  

Once Device A recieves the payload again. They will verify it and mark the session as connected

Then packet id 5 back to Device B stating that the encryption is safe to be be secured

```json5
{
    protocol: 0,
    packet: 4
    content: true
}
```

### #### Diagram

```mermaid
sequenceDiagram
    autonumber
    participant A as Device A
    participant B as Device B
    note over A,B: Say Hello and ensure both devices know each other 
    A->>B: Hello Packet ID: 0
    B->>A: Hello Packet ID: 0
    note over A: Generate a random byte array. 256 is what is used in the Rust Impl
    A->>B: Packet ID 5 With the encrypted Byte Array
    note over B: Decrypts the byte array with its private key. And Encrypts it with the public key for Device A
    B->>A: Packet ID 5 with the payload
    note over A: Decrypt payload and check it with the prevously generated byte array
    alt if data matches:
        note left of A: mark the local session as secure. After sending packet
        A->>B: Packet ID 6. With true in the content.
        note right of B: Once recieved Packet ID 6 mark the session as secure. 
        note over A,B: The session is secure. The app can run now
    else data does not match or failed to decrypt:
        note over A,B: treat this as a bad pairing and try reparing
        A->>B: Packet ID 6 with false.
    end
```

### Device Pairing

#### Pair Request

The first step in pairing two devices together is to see device B wants to pair with you. This is done by first opening a TCP connection. Then sending the hello packet(id:0) and sending a packet 3

```json5
{
    protocol: 0,
    packet: 3,
    content: {
        device_name,
        Option<Bytes>/// Optionally you can include byte array of any other data you want. 
    }
}
```

If device B wants to connect it will send a packet 3 with its public_key and and optional test string

##### Optional Key Verifcation

The Optional Test. If you want to ensure that your key is the one sent to the other side. Pass a byte array for the test. This must be encrypted with the same key. On the other side they will need to know this string. They will use the public key provided to encrypt the known string If they are test the encrypted test and the known string(encrypted) are the same. The key has not been compromised.
Once the other side has a received the key they will use the public key to send you their key. And at this point both devices are paired They can move onto the Key Check to mark the session as secure.

### Pair Request Cont

Once you recieve this you can verify the test string if needed. Then sending a SendKey(Packet ID: 4) back to the Devce B: With your Public Key and the Test String encrypted with your key. You can now your change your session status from Pairing to Pending Encryption. Also save Device B as a paired device

Once the other side recieves this packet it will do the test and if everything is ok mark the session as pending encryption. Marking Device A as a paired device

At this point Device B will need to send a key check to change the session from Pending Encryption to Connected

##### Diagram

```mermaid
sequenceDiagram
    autonumber
    participant A as Device A
    participant B as Device B
    note left of A: Mark your Session as pairing
    A-->>B: Packet ID 3 with your device name
    alt device b denies:
        B-->>A: Denied. Connection Closed
    end
    note right of B: Mark Session as pairing
    note over B,A: you can include the test array if wanted
    B-->>A: Packet ID 4 with public key
    alt if test array provided:
        note over A: Take the known test string and encrypt it with the recieved public key
        note over A: Ensure that it matches the data provided
        alt if test failed:
            note over A,B: Key Compromised
        end
    end
    note over A: Mark Session PendingEncryption
    note over A: Save the pair infomation
    note over A: Encrypt the known test array with the public key being sent to B
    A-->>B: Packet ID 4 with Device A's public key
    alt if test array provided:
        note over B: Take the known test string and encrypt it with the recieved public key
        note over B: Ensure that it matches the data provided
        alt if test failed:
            note over B, A: Key Compromised
        end
    end
    note over B: Save the pair infomation

    note over B: Mark session as Pending Encryption 
    B-->>A: Key Check

    note over A,B: Make sure the follow the complete key check process. 
```
