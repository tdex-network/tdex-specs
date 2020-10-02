# BOTD #2: Transport Protocol


## Overview

All communications between two *peers* SHOULD be encrypted in order to provide confidentiality for all transcripts and is authenticated in order to avoid malicious interference. 



### Transport announcement

Each *peer* MUST decide and MUST announce which transport protocol wants to use for the connection, one or more combined between:


* Clear text `insecure`
* Onion service `onion`
* Server-side TLS `TLS`
* Mutual TLS `mTLS`
* Noise_XK_secp256k1_ChaChaPoly_SHA256 `noise`


Each peer SHOULD be reachable on the default TCP port **9945** and, if so, MUST expose then a plaintext HTTP/2 endpoint that announces the chosen transports responding with an `AvailableTransport` protobuf message, setting the response `ContentType` as `application/protobuf`


#### Data Structures

```protobuf
syntax = "proto3";

enum TransportType {
	INSECURE = 0;
	ONION = 1;
	TLS = 2;
	MTLS = 3;
	NOISE = 4;
}

message Transport {
	TransportType type = 1;
	string name = 2;
	bytes data = 3;
}

message AvailableTransport {
	repeated TransportType transport = 1;
}

```

Each `Transport` has an optional `data` field that can be used to return additional metadata. Eg. A static public key in case of `noise` or a self-signed TLS certificate in case of `mTLS`









	
