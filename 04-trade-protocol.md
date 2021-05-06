# BOTD #4: Trade protocol

The Trade protocol defines the public interface of a non-custodial exchange on top of an Elements-based chain such as [Liquid Network](https://liquid.net). 


## Overview

The centralized exchange main business is providing a venue for *traders* and *market makers* to register their offers to buy and sell in an order-book. In order to execute the orders, both are required to deposit funds and trust the exchange as custodian.  

**TDEX** aims to create an open network that connects *traders* and *market makers* directly to each other, without the need of a centralized custodian, exploiting the atomic swap capability of Elements-based chains for executing trades in a trustless fashion.

The **market makers** provide always-on endpoints and put liquidity in various asset pairs forming a specified interface called **Market**.  Each *Market* holds reserves of a **base asset** and a **quote asset**.

Anyone can become a **liquidity provider** and contribute with reserves in a non-custodial manner running an always-on endpoint to process trading requests. This is different than buying or selling; it requires depositing an equivalent value of both base and quote assets and select an automated market making strategy. 

A **trader** connects to the provider using the [secure transport defined in the BOTD #2](02-transport-protocol.md), fetches the current *market price* defined by the provider's strategy and makes an atomic swap using the [swap protocol defined in the BOTD #3](03-swap-protocol.md). Limit orders could be supported as well, delaying the time of execution of the swap proposal in the future.


## Trade

1. The **Provider** deposits two reserves for each asset forming a *Market*, defined as a pair of BASE ASSET and QUOTE ASSET, and expose a public reachable endpoint.
2. The **Trader** fetches from the **Provider** the list of supported pairs and linked provider's fees.
3. The **Trader** passing a supported market in the request recognized by the hashes of two assets, fetches the current **market rate**
4. The **Trader** proposes a new **swap request** using the price given and awaits for Provider's response.
5. If the **Provider** accepts the terms will send back a **swap accept** message containing the partially signed transaction.
6. The **Trader** sends the signed transaction to finalize the trade using the **swap complete** message.
7. The **Provider** will finalize and broadcast the transaction to the network for on-chain settlement.

> NOTICE: The Trader could skip point 6-7 and right away broadcast the transaction himself. Even if valid would be better for clients software to let the Provider be aware of the finalized transaction id rather than watch the blockchain to scan for known swaps.


### Data Structures 

* Service Interface

```protobuf
service Trade {
  rpc Markets(MarketsRequest) returns (MarketsReply);
  rpc Balances(BalancesRequest) returns (BalancesReply);
  rpc MarketPrice(MarketPriceRequest) returns (MarketPriceReply);
  rpc TradePropose(TradeProposeRequest) returns (stream TradeProposeReply);
  rpc TradeComplete(TradeCompleteRequest) returns (stream TradeCompleteReply);
}
```

* Messages 

```protobuf
message MarketsRequest {}
message MarketsReply { repeated MarketWithFee markets = 1; }

message BalancesRequest { Market market = 1; }
message BalancesReply { repeated BalanceWithFee balances = 1; }

message MarketPriceRequest {
  Market market = 1;
  TradeType type = 2;
  uint64 amount = 3;
  string asset = 4;
}
message MarketPriceReply { repeated PriceWithFee prices = 1; }

message TradeProposeRequest {
  Market market = 1;
  TradeType type = 2;
  SwapRequest swap_request = 3;
}
message TradeProposeReply {
  SwapAccept swap_accept = 1;
  SwapFail swap_fail = 2;
  uint64 expiry_time_unix = 3;
}

message TradeCompleteRequest {
  SwapComplete swap_complete = 1;
  SwapFail swap_fail = 2;
}
message TradeCompleteReply {
  string txid = 1;
  SwapFail swap_fail = 2;
}
```

* Custom Types 

```protobuf
enum TradeType {
  BUY = 0;
  SELL = 1;
}
message Fee {
  int64 basis_point = 1;
  Fixed fixed = 2;
}
message Fixed {
  int64 base_fee = 1;
  int64 quote_fee = 2;
}
message Balance {
  uint64 base_amount = 1;
  uint64 quote_amount = 2;
}
message BalanceWithFee {
  Balance balance = 1;
  Fee fee = 2;
}
message Market {
  string base_asset = 1;
  string quote_asset = 2;
}
message MarketWithFee {
  Market market = 1;
  Fee fee = 2;
}
message Price {
  float base_price = 1;
  float quote_price = 2;
}
message PriceWithFee {
  Price price = 1;
  Fee fee = 2;
  uint64 amount = 3;
  string asset = 4;
  Balance balance = 5;
}
```


## Glossary 

* **Liquidity Provider**: Holds reserves of pegged Bitcoin (eg. L-BTC) and an associated Elements asset in his non-custodial wallet, running automated market-making strategies both with or without an oracle. Providers are incentivized to be always on and need to expose a public reachable endpoint either via clearnet or using a [Onion hidden service](https://2019.www.torproject.org/docs/tor-onion-service.html)

* **Market**: A single provider putting liquidity into an asset pair forms a market defines as **BASE_ASSET-QUOTE_ASSET**. Multiple markets can co-exist, although this is less beneficial for signaling offers to traders. 

* **Trader**: Proposes new swaps using the provider's market rate. A trader can come and go, he only needs to support the [swap protocol defined in the BOTD #3](03-swap-protocol.md) and he can swap between the two assets in the **Market** in either direction by adding to the liquidity reserve of one and withdrawing from the reserve of the other. Traders can either connect directly to a **provider** if they already know the endpoint.

* **Pool**: Providers can register into a distributed service mesh with other providers pooling together liquidity. This acts as a first responder for traders to lookup for provider's aggregated offers. A provider could run alone OR in a pool, but not both at the same time with the same reserves.

* **Swap fee**: A small percentage of the amount of the trade can be taken out by the provider and added to the reserves, besides the network fees. Since the provider is conveniently in charge of paying network fees, he could also charge an additional fixed fee amount to the trade to be partially reimbused for this expense. While the *BASE_ASSET-QUOTE_ASSET* reserve ratio is constantly shifting, fees make sure that the total combined reserve size increases with every trade.
Guaranteed arbitrage opportunities from price fluctuations should push a steady flow of transactions through the system and increase the amount of fee revenue generated.

* **Automated Market Making**: A liquidity provider has full control over the market making strategy to apply needed to calculate the **market rate** at which to accept trades. That being said, there is a possibility to apply an automated market-making relying only on the reserves balances and the amount requested by the trader, without the need to connect to an external price feed. One of the most famous algorithms is called *constant product market-making*. In short, this model generates a full order-book based on an initial price for the market. Every transaction that occurs on this market will adjust the prices of the market accordingly. It's a basic supply and demand automated market making system. 
