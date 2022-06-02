# A standard packet

```json
{
   "packet_id": u8
   "content": [u8]
}
```

```mermaid
participant C as Client
participant S as Server

C-->>S: TCP socket connected
note over C, S: Over unencrypted connection
C-->>S: Hello I am <device id>.
S-->>C: Here's my <device id> and <pair status>
note over C: Check if Client knows Server by Device ID
alt are paired
note over C,S: Over Encrypted connection
C-->>S: Send Key Check
S-->>C: Reply with Key Check
note over C, S: Application is now free to communicate properly
else
C-->S: Send Device Pair
S-->C: Reply with <Public Key>
note over C: Client will now encrypt messages with the Server's public key
C-->S: Reply with <Public Key>
note over S: Server will now encrypt messages with the Client's public key
note over C,S: Jump to Key check now that they are paired
end
```


# Pre-defined Packets

## Hello from the other side

### Send

```json
{
   "packet_id": 0
   "content": [{DEVICE ID}]
}
```

### Response 

```json
{
   "packet_id": 0
   "content": "{DEVICE_ID, IS_PAIRED}"
}
```

If Device A also agrees it knows B. Go into encrypted talk. Start with KEY_CHECK

If either one says no. Go into Pair Talk

## Device Pair

### Send

```json
{
   "packet_id": 1
   "content": {}
}
```

Other Device creates a public and private key

### Response 

```json
{
   "packet_id": 1
   "content": {PUBLIC_KEY}
}
```

Once it recieves the certifcate from the other device. Generate a your own public and private key. 

This packet is encrypted
### Send

```json
{
   "packet_id": 1
   "content": {PUBLIC_KEY}
}
```
Wait for a key check

## Key Check



### Send 

Send a message encrypted using the private key

```json
{
   "packet_id": 2
   "content": []
}
```

Wait for a response that is encrypted. You should be able to decrpyt via the key you have saved.

### Response 

```json
{
   "packet_id": 2
   "content": {}
}
```
