# Random notes on contract design things


# Using Struct Variants in Init/Exec/Query messages

- I find that using Struct Variants in my Init/Execute/Query functions saves me a lot of hassle when working with TypeScript/JavaScript on the Front End

- **Instead of doing something like this**
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {

    PlaceOrder(u64, PlaceOrderMessage),

    BuyOrder(BuyOrderMessage),
}
```

- **I would rather do this**
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {

    PlaceOrder { number_for_something: u64, place_order_msg: PlaceOrderMessage },

    BuyOrder { buy_order_msg: BuyOrderMessage },
}
```

- **Where my JSON-serialized message would look like this in my TS/JS code**
```js
{ 
    place_order: { 
        number_for_something: 82, 
        place_order_msg:...
    },
}
```

- **Or like this in on the command line**
```bash
'{"place_order": {"number_for_something": 72, "place_order_msg": {...}}}'
```

- This is just my own preference to keep things consistent. It requires me to write some extra code, but 
keeps me from needing to mess around with serde tags

- If you want to read more about *why* -> [Serde docs](https://serde.rs/enum-representations.html)

---
---

# Getting storage data from a map: in prog

- If you are using storing data within a `Map`, CosmWasm has multiple tools to get data from that map

- You can of course get a specific entry in a Map if you have the key 

```rust
pub struct User {
    name: String,
    age: u32,
}

// &str = Key to data
const PEOPLE: Map<&str, User> = Map::new("people");

fn save_people(deps: DepsMut) -> Result<Response, ContractError> {

    let john = User { name: "John".to_string(), age: 29 };
    // save john
    PEOPLE.save(deps.storage, "john" , &john)?;

    let ali = User { name: "Ali".to_string(), age: 22 };
    // save ali
    PEOPLE.save(deps.storage, "ali" , &ali)?;
}

fn load_person(deps: DepsMut) -> Result<Response, ContractError> {

    let ali = User { name: "Ali".to_string(), age: 22 };

    let ali_key = "ali";

    let ali_loaded_from_storage = PEOPLE.load(deps.storage, &ali_key)?;

    assert_eq!(ali, ali_loaded_from_storage)

}

```

You can also get multiple items from the map by using `.range`

`.range` takes in storage, MIN, MAX, and order

- If order = `Order::Ascending`, items returned will be ordered **Most recent -> Oldest**


# Getting an wallet's token balance (Native & Cw20)

**Native**
```rust
let resp: Coin = deps.querier.query_balance(user_wallet, "ujuno")?;

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

let coin_resp: Coin = resp.amount;
```



# IM

