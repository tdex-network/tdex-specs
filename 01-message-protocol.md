# BOTD #1: Message Protocol

### Definitions

A **connection** is a reliable, bidirectional communication channel between two peers that provides security.

**Security** in this context means that all communications (after an initial handshake) are encrypted, and that the identity of each peer is cryptographically verifiable by the other peer.

All messages sent between peers have headers. 

The payloads are serialized using [**Protocol Buffers**](https://developers.google.com/protocol-buffers/), a language-neutral, platform-neutral, extensible mechanism for serializing structured data.


### The unencrypted message

1. **lenght**
 * Size: 4 bytes 
 * Data type: uint32
 * Description: Number of bytes in payload
2. **checksum**
 * Size: 4 bytes
 * Data type: bytes
 * Description: First 4 bytes of SHA256(payload)
3. **payload**
 * Size: lenght
 * Data type: bytes
 * Description: The actual data 

 
### The encrypted message

1. **lenght**
 * Size: 4 bytes 
 * Data type: uint32
 * Description: Number of bytes in payload
2. **encrypted payload**
 * Size: lenght
 * Data type: bytes
 * Description: The encrypted data 

