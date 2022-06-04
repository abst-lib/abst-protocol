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

#### Default Packet Type.

```json5
{ 
  "protocol": 0, // Used to differentiate the protocol being used. 0 is for ABST
  "packet_id": 0, // Each 
  "content": [] // The content being sent
}
```

A device to device packet requires part one that is the packet id. A byte 0-255. The content is any data you would like it to be. 

#### Realm Relay Packet

Used to tell the Realm to relay the packet to another location

```json5
{ 
"protocol": 0, // 0 for device to device, 1 for device to peer
"packet_id": 0, // The packet id. On 1 and 2 they are 0 meaning pass content along
"content": {
   "target": UUID // As two u64.
   "inner_content": {} //This is a normal device to device packet as byte array encrypted as  with the device key
 }
}
```

### Packet Defination

#### Protocol Definations

Outside of the following values they should be used for application level data

| 0   | device to device |
| --- | ---------------- |
| 1   | device to peer   |
| 2   | device to realm  |

### Defined Packet Types

These packet ids are used in the ABST protocol

#### Device to Device - protocol id: 0

| 0   | Heart beat                                            |                                                                                 |
|:--- |:----------------------------------------------------- |:------------------------------------------------------------------------------- |
| 1   | Hello. Allows for devices to check if they are paired |                                                                                 |
| 2   | Pair Process                                          |                                                                                 |
| 3   | Key Check                                             |                                                                                 |
| 4   | Give Peer Group Access                                | Include the Public and Private key for the peer group. Also include the peer id |

#### Device to Realm - protocol id: 2

| 0   | Hello, I am here. Also I am open for connecting | Include the Device ID                  |
|:--- |:----------------------------------------------- |:-------------------------------------- |
| 1   | Connect to Device                               | Include your ID and the target device. |
|     |                                                 |                                        |

#### Realm to Device - protocol id: 2

| 0   | Back Heartbeat                                     |     |
|:--- |:-------------------------------------------------- |:--- |
| 1   | Notifies the target is being connected to a device |     |
|     |                                                    |     |

#### Peer to Device- protocol id 1

Packets that are sent to peer groups. These are sent to the common realm they all share

| 0   | Create a Peer Group | Include the Peer Group ID                                                                                                                       |
|:--- |:------------------- |:----------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Join Peer Group     | Include your peer group id. Your ID. And it will be sent to all devices notifying your arrival. If the encryption is bad. Then you can not join |
| 2   | I am here           | Just notifies the Realm that you are here.                                                                                                      |
|     |                     |                                                                                                                                                 |
|     |                     |                                                                                                                                                 |