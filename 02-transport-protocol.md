# BOTD #2: Transport Protocol


## Overview

All communications between two *peers* SHOULD be encrypted in order to provide confidentiality for all transcripts and authenticated in order to avoid malicious interference.

The type of transport used by a peer can be easily determined by the canonical URL it can be reached at (ie. http -> insecure, https -> TLS/mTLS, .onion -> onion).

### Message type announcement

Liquidity providers MUST decide and announce the accepted content type for incoming HTTP request messages.

Peers connecting to liquidity providers should ask for the accepted content types and use one of them to prevent using a non supported one.

A liquidity provider can accept requests of different content types but **MUST** support at least `application/json`.


#### Data Structures

* Service interface
	```protobuf
	syntax = "proto3";
	import "google/api/annotations.proto";

	service Transport {
		rpc SupportedContentTypes(SupportedContentTypesRequest) returns (SupportedContentTypesReply) {
			option (google.api.http) = {
				get: "/v1/supported_types"
			};
		}
	}
	```

* Messages
  ```protobuf
	enum ContentType {
		JSON = 0;
		GRPC = 1;
		GRPCWEB = 2;
		GRPCWEBTEXT = 3;
	}

	message SupportedContentTypesRequest {}
	message SupportedContentTypesReply {
		repeated ContentType accepted_types = 1;
	}
	```








	
