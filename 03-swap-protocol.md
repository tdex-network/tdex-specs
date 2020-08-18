# BOTD #3: Swap protocol

## Overview

The **swap protocol** defines the process wich two parties conclude an atomic swap using an interactive exchange of signed messages and Elements transaction.

Identifying with *Alice* as the **Proposer** and *Bob* the **Responder**:

1. Alice connects to Bob trough secure transport layer and encrypted connection.
2. Alice propose a swap crafting an unsigned transaction and a message defined as sending `AMOUNT_P` of `ASSET_P` and receiving `AMOUNT_R` of `ASSET_R`. If confidential, the blinding keys need to be included.
3. Alice sends to Bob the `SwapRequest` message containing the unsingned transaction. An additional input and eventual change output needed to pay *half* of the network fees is included by Alice in the transaction.
4. Bob, if accepts the terms, funds the swap and partially signs the proposed transaction and includes his blinding keys too.
5. Bob sends back to Alice the `SwapAccept` message containing the partially signed transaction. An additional input and eventual change output needed to pay the remaining *half* of the network fees is included by Bob in the transaction.
6. Alice parses the accepted swap and signs the transaction.
7. Alice sends to Bob the `SwapComplete` message containing the signed transaction.
8. Ideally Bob finalizes and broadcast the transaction to the Liquid network.

## Swap

### Data Structures

```protobuf
syntax = "proto3";

message SwapRequest {
  // Random unique identifier for the current message
  string id = 1;
  // The proposer's quantity
  uint64 amount_p = 2;
  // The proposer's asset hash
  string asset_p = 3;
  // The responder's quantity
  uint64 amount_r = 4;
  // The responder's asset hash
  string asset_r = 5;
  // The proposer's unsigned transaction in PSET format (base64 string)
  string transaction = 6;
  // In case of a confidential transaction the blinding key of each confidential
  // input is included. Each blinding key is identified by the prevout script
  // hex encoded.
  map<string, bytes> input_blinding_key = 7;
  // In case of a confidential transaction the blinding key of each confidential
  // output is included. Each blinding key is identified by the output script
  // hex encoded.
  map<string, bytes> output_blinding_key = 8;
}

message SwapAccept {
  // Random unique identifier for the current message
  string id = 1;
  // indetifier of the SwapRequest message
  string request_id = 2;
  // The partial signed transaction base64 encoded containing the Responder's
  // signed inputs in a PSET format
  string transaction = 3;
  // In case of a confidential transaction the blinding key of each confidential
  // input is included. Each blinding key is identified by the prevout script
  // hex encoded.
  map<string, bytes> input_blinding_key = 4;
  // In case of a confidential transaction the blinding key of each confidential
  // output is included. Each blinding key is identified by the output script
  // hex encoded.
  map<string, bytes> output_blinding_key = 5;
}

message SwapComplete {
  // Random unique identifier for the current message
  string id = 1;
  // indetifier of the SwapAccept message
  string accept_id = 2;
  // The signed transaction base64 encoded containing the Proposers's signed
  // inputs in a PSET format
  string transaction = 3;
}

message SwapFail {
  // Random unique identifier for the current message
  string id = 1;
  // indetifier of either SwapRequest or SwapAccept message. It can be empty
  string message_id = 2;
  // The failure code. It can be empty
  uint32 failure_code = 3;
  // The failure reason messaged
  string failure_message = 4;
}
```

### SwapRequest 

The `SwapRequest` message is sent by the **Proposer** to the **Responder** to start the swap negotiation. The transaction is as PSET base64 encoded string containing the Proposer's inputs and outputs (amount_r and eventual change).

### SwapAccept 

The `SwapAccept` message is sent by the **Responder** to the **Proposer** to accept the swap request.

### SwapComplete

The `SwapComplete` message is sent by **Proposer** to the **Responder** to announce the successful completion of the swap. 

### SwapFail

The `SwapFail` message can be sent by either side of the swap protocol, at any time, to announce the swap termination.
`failure_code` is an optional parameter for specifying the failure reason. TBD


