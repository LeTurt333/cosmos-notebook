# CosmWasm General

**Table of Contents**

- ### [CONTRACT DEPLOYMENT](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#--contract-deployment--)
  - [Basics of the workflow](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#basics-of-the-workflow)
  - [Build/Optimize a contract](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#building--optimizing-a-contract-using-rust-optimizer)
  - [Upload a contract to the blockchain](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#uploading-your-contract-to-a-blockchain)
  - [Instantiate a contract](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#instantiating-your-contract)
  - [Execute a contract](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#executing-your-contract)
  - [Query a contract](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#querying-your-contract)
- ### MISC
  - [jq](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#using-jq-to-better-format-output-of-commands)

</br>

---

</br>

---

</br>

---

</br>

# ~-~ CONTRACT DEPLOYMENT ~-~

# Basics of the workflow

1. **Write your smart contract (Guide on getting started - [Callum/zero-to-hero](https://github.com/Callum-A/cosmwasm-zero-to-hero))**
2. [**Build/Optimize your contract**](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#building--optimizing-a-contract-using-rust-optimizer)
3. [**Upload your contract to a blockchain**](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#uploading-your-contract-to-a-blockchain)
4. [**Instantiate your contract**](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#instantiating-your-contract)
5. [**Execute your contract**](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#executing-your-contract)
6. [**Query your contract**](https://github.com/LeTurt333/cosmos-notebook/blob/main/cw-general/cw-general.md#querying-your-contract)

</br>

---
---

</br>

# Building/Optimizing a contract (using [Rust Optimizer](https://github.com/CosmWasm/rust-optimizer))

- Prerequisite: Install [Docker](https://docs.docker.com/engine/install/)

</br>

1. Start Docker

2. Navigate to the root of your contract repo in a terminal 
```bash
name@computer:/home/contract-repo
```

3. And run this command
```bash
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.6
```

4. This will create a new folder `contract_repo/artifacts` that will contain your optimized contract binary `/artifacts/mycontract.wasm`

</br>

---
---

</br>

# Uploading your contract to a blockchain

**Note**: Depending on the tooling / blockchain you're using, this may look different. For this example I am using **Juno** and **junod**

**Note**: Make sure that in your `home/.juno/config/client` (or whatever chain you're using) you have set your `node` and `chain-id` correctly

`node` will need to be an active rpc, `chain-id` will be the name of the chain you're uploading to (there is a difference between mainnet vs testnet so keep that in mind). [List of active rpcs in Cosmos ecosystem](https://github.com/cosmos/chain-registry)

</br>

1. Go to your `my_contract/artifacts` directory created in the previous step

2. Run this command, replacing the items in <_> with your relevant information

```bash
junod tx wasm store <MY_CONTRACT.wasm> --from <MY_WALLET> --gas-prices 0.
1ujunox --gas auto --gas-adjustment 1.3 -y -b block
```

3. Here's an example of what this command should look like

```bash
junod tx wasm store my_contract.wasm --from juno17dn5e2n6w60pzyxeq79apr05r6jzfw7wha8558 --gas-prices 0.
1ujunox --gas auto --gas-adjustment 1.3 -y -b block
```

4. If this command succeeds, it will spit out a big chunk of JSON inside your terminal. You'll be looking for the part that says `code_id: 1234`. **Save this number!** You'll need it when instantiating your contract

</br>

---
---

</br>

# Instantiating your contract

**Note**: Depending on the tooling / blockchain you're using, these commands may look different. For this example I am using **Juno** and **junod**

</br>

1. Run the following command, replacing `1234` with the code_id you saved in the last step, and also replacing `'{}'` with whatever your Instantiate message is defined as

```bash
junod tx wasm instantiate 1234 '{}' --label "leturts_contract" --from <YOUR WALLET ADDRESS> --admin <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -b block -y
```

2. You'll notice that the actual instantiate message `'{}'` here is blank. If you have modified your instantiate message in your contract to something that cannot be blank, you'll need to put something in there. Here's an example

</br>

If your instantiate message looks like this
```rust
pub struct InstantiateMsg {
    pub name_of_something: String,
}
```

Your command will look something like this
```bash
junod tx wasm instantiate 1234 '{"name_of_something": "the name"}' --label "leturts_contract" --from <YOUR WALLET ADDRESS> --admin <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -b block -y
```

</br>

3. If the instantiation succeeds, another big chunk of JSON will be spit out in your terminal. Inside it you'll find something like `address: juno1xxx`. This is the contract address! You can now Execute or query your contract. Nice.

</br>

---
---

</br>

# Executing your contract

1. Using your contract address from the previous step, run the following command

```bash
junod tx wasm execute <YOUR CONTRACT ADDRESS> '{"increment": {}}' --from <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -y -b block
```

2. Here is another example with a different execute message

</br>

Execute message
```rust
pub enum ExecuteMsg {
    AddData { data: String },
}
```

Execute command
```bash
junod tx wasm execute juno168ctmpyppk90d34p3jjy658zf5a5l3w8wk35wht6ccqj4mr0yv8s4j5awr '{"add_data": {"data": "data I want to add"}}' --from <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -y -b block
```

</br>

3. If you want to view some details about your tx, copy the tx hash in the JSON output (or just your wallet address) and check it out with a block explorer like [Mintscan](https://hub.mintscan.io/overview) or [Pingpub](https://ping.pub/)

</br>

---
---

</br>

# Querying your contract

Finally, let's query your contract to get some info about it's state

</br>

1. Using your contract address, run the following command in a terminal

```bash
junod query wasm contract-state smart <YOUR CONTRACT ADDRESS> '{"get_count": {}}'
```

</br>

---

</br>

---

</br>

---

</br>

# ~-~ MISC ~-~

# Using jq to better format output of commands

> "jq is like sed for JSON data - you can use it to slice and filter and map and transform structured data" - jq docs

Personally I like to use jq for commands that output JSON to make life easier

For example, if you run the following command in your terminal, the output will look something like this

</br>

Command
```bash
junod status
```

Output
```bash
{"NodeInfo":{"protocol_version":{"p2p":"8","block":"11","app":"0"},"id...
```

</br>

It will be *very* long and hard to read. Kind of like your cat walked across your keyboard when you weren't looking...

You can clean up the output by using jq with your command. Try it out!

</br>

Command
```bash
junod status | jq
```

Output
```json
{
  "NodeInfo": {
    "protocol_version": {
      "p2p": "8",
      "block": "11",
      "app": "0"
    },
    "id":...
```
Eyes == Happy

</br>

You can also use jq to filter output for specific information you're looking for

</br>

Command
```bash
junod status | jq '.NodeInfo.protocol_version'
```

Output
```json
{
  "p2p": "8",
  "block": "11",
  "app": "0"
}
```

This comes in useful when uploading a contract and you just want the code ID for example


