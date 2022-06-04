### Send to Realm Packet

```json
{
   "packet_id": 0
   "content": []
}
```


#### Realm packet targetting another device

```json
{  
   "packet_id": 1, //Send To another device
   "target_device": UUID // Would be null if being sent to the realm
   "content": { // Encrypted with the public key from server. Any packet from the Device_Device  Protocol
          "packet_id": 0
          "content": [{}]
   }
}
```

##### Becomes this when Realm to

```json
{  
    "packet_id": 0,
    "content": [{}]
}
```



#### Realm packet targetting another device

```json
{  
   "packet_id": 2, //Hold Data
   "target_device": UUID // Would be null if being sent to the realm
   "content": { // Encrypted with the public key from server. Any packet from the Device_Device  Protocol
          "packet_id": 0
          "content": [{}]
   } v
}
```

