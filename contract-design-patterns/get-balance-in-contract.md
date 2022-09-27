# Getting an wallet's token balance (Native & Cw20)

Example of how you can query a wallet for it's balance within a smart contract...if you ever wanted to do this

**Native Tokens**
```rust
let resp: Coin = deps.querier.query_balance(some_wallet_address, "ujuno")?;

let user_amount = resp.amount;
```

**CW20**
```rust
let resp: BalanceResponse = deps
    .querier
    .query(&QueryRequest::Wasm(
        WasmQuery::Smart{ 
            contract_addr: cw20_contract, 
            msg: to_binary(&format!("Balance {{ address: {} }}", wrapper.sender))?}
    ))?;

let coin_resp = resp.amount;
```