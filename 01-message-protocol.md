# BOTD #1: Message Protocol

### Definitions

A **connection** is a reliable, bidirectional communication channel between two peers that provides security.

**Security** in this context means that all confidential communications SHOULD be encrypted, and that the identity of each peer SHOULD be cryptographically verifiable by the other peer.

The messages exchnaged between peers are serialized using [**Protocol Buffers**](https://developers.google.com/protocol-buffers/), a language-neutral, platform-neutral, extensible mechanism for serializing structured data.

 