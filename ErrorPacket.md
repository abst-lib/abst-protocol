# Error Packet ID 1

This packet id is reserved in protocols 0,1,2 to mean and error has occured

### Error Content

```json5
includes_ref: bool, //rather or not it references another packet
protocol_ref: u8, // Will not be included if `include_ref` false
packet_ref: u8, // Will not be included if `include_ref` false'
error_id: u8, // This value will mean something based on the `ref` values
debug_message: Optional<String>
```

If we have a ref we can compare the error_id to the error_id list found on that packet's documentation




