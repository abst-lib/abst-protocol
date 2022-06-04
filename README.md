# abst-protocol


##
## Concepts 
The general overview of concepts found within ABST
### Realm

A Realm is a server whose sole purpose is to relay packets between devices that are in the same physical space, but do not share the same network connection or are firewalled and cannot communicate directly. Realms are designed in a way that they do not understand what is being passed between devices

### Device

A device here represents an internet connected hardware equipment running the program using this library. Devices are able to find each other in the same network and even communicate between each other. Via a Realm

### Network

A network is a group of devices. While the devices can be in the same physical space, it is not required and they should be able to communicate between each other even when \*apart\* thanks to a Realm.

### Peer Group

#### Peer Groups are a special type of Networks that are able to store encrypted Key/Value pairs in a Realm. They are only available when using the Peer-to-Peer mode and due to the nature of it, all data is tied to a specific Realm and can't be handed off automatically in a secure fashion.

## Network Overview
The network will be a layer 7 TCP connection. 

### Packets
Packets are going to be small objects in the MessagePack format. ABST has two Packet Format
#### Device to Device Packet

```
{ 
"packet_id": 0,
"content": [{}]
}
```
A device to device packet requires part one that is the packet id. A byte 0-255. The content is any data you would like it to be. 

#### Device to Realm Packet
```json
{ 

"packet_id": 0, *//Send To another device*
"target_device": UUID, *// Would be null if being sent to the realm*
"peer_group": UUID, // Would be null if targeting a device or the realm*
"content": { *// Encrypted with the public key from the server. Any packet from the Device\_Device  Protocol*
  "packet_id": 0
  "content": [{}]
 }
}
```
`Target_device` and `peer_group` are both optional if the packet is going to the realm. 

The content data is encrypted with the key previously exchanged between the two devices. Can not be decrypted by the realm.

### Reserved Packet IDs

These packet ids are reserved for the use of the ABST protocol

#### Device to Device


|0|Heart beat||
| :- | :- | :- |
|1|Hello. Allows for devices to check if they are paired||
|2|Pair Process||
|3|Key Check||
|4|Give Peer Group Access|Include the Public and Private key for the peer group. Also include the peer id|

#### Device to Realm


|0|Hello, I am here. Also I am open for connecting|Include the Device ID|
| :- | :- | :- |
|1|Connect to Device|Include your ID and the target device. |
|2|Send To Another Device|This is the realm packet defined earlier. |
#### Realm to Device
Realm to device packets are only available if the connection state is not connected to another device


|0|Back Heartbeat||
| :- | :- | :- |
|1|Notifies the target is being connected to a device||
||||
#### Peer to Device
Packets that are sent to peer groups. These are sent to the common realm they all share


|3|Create a Peer Group|Include the Peer Group ID|
| :- | :- | :- |
|4|Join Peer Group|Include your peer group id. Your ID. And it will be sent to all devices notifying your arrival. If the encryption is bad. Then you can not join|
|5|I am here|Just notifies the Realm that you are here. |
|6|Peer Group Message|This is the realm packet defined earlier. Using the peer group ud|
####