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

	service TransportService {
		rpc SupportedContentTypes(SupportedContentTypesRequest) returns (SupportedContentTypesResponse) {
			option (google.api.http) = {
				get: "/v1/transport"
			};
		}
	}
	```

* Messages
  ```protobuf
	enum ContentType {
		CONTENT_TYPE_JSON = 0;
		CONTENT_TYPE_GRPC = 1;
		CONTENT_TYPE_GRPCWEB = 2;
		CONTENT_TYPE_GRPCWEBTEXT = 3;
	}

	message SupportedContentTypesRequest {}
	message SupportedContentTypesResponse {
		repeated ContentType accepted_types = 1;
	}
	```








	
