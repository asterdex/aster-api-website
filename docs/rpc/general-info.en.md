- [General Info](#general-info)
  - [General API Information](#general-api-information)
- [Chain RPC Endpoints](#chain-rpc-endpoints)
  - [Get Balance](#get-balance)
  - [Get Open Orders](#get-open-orders)
  - [Get User Fills](#get-user-fills)

## **General Info**

## General API Information

* All endpoints use JSON-RPC 2.0 protocol via HTTP POST.
* The base endpoint is: **https://tapi.asterdex.com/info**
* All requests must set `Content-Type: application/json`.
* The `blockTag` parameter accepts `"latest"` to query the most recent state.
* All time-related fields are in milliseconds.

> **Note:** The following endpoints only return data when the user has **privacy mode disabled**. If privacy mode is enabled, no data will be returned.

---
