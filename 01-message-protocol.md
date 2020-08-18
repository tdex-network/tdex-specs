# BOTD #1: Message Protocol

### Definitions

A **connection** is a reliable, bidirectional communication channel between two peers that provides security.

**Security** in this context means that all confidential communications (after an initial handshake) are encrypted, and that the identity of each peer is cryptographically verifiable by the other peer.

All encrypted messages sent between peers have a payload and lenght.

The messages are serialized using [**Protocol Buffers**](https://developers.google.com/protocol-buffers/), a language-neutral, platform-neutral, extensible mechanism for serializing structured data.


### Data structures

```protobuf
syntax = "proto3";

message SecretMessage {
  // Number of bytes in payload
  uint32 lenght = 1;
  // The encrypted Protobuf message serialized as bytes
  bytes payload = 2;
}
```

 