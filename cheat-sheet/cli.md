# Cheat Sheet - CLI

---

# Optimized compile -> Upload -> Instantiate

### Compile contract/workspace

***Optimized compile - Single contract***
```bash
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.8
```

***Optimized compile - Contract workspace***
```bash
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/workspace-optimizer:0.12.8
```

### Upload compiled wasm to blockchain

```bash
junod tx wasm store <MY_CONTRACT.wasm> --from <MY_WALLET> --gas auto --gas-prices 0.
1ujunox --gas-adjustment 1.3 -y -b block
```

### Instantiate uploaded contract

```bash
junod tx wasm instantiate 1234 '{}' --label "leturts_contract" --from <YOUR WALLET ADDRESS> --admin <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -y -b block
```

---

# List of denoms in bank module

```bash
junod query bank total -o json | jq -r '.[] | .[] | .denom'
```

# Query contract-state smart & decode in 1 line

```bash
junod query wasm contract-state smart <CONTRACT_ADDRESS> '{"query_msg": {}}' -o json | jq -r '. | .[] | @base64d'
```
pretty print JSON
```bash
junod query wasm contract-state smart <CONTRACT_ADDRESS> '{"query_msg": {}}' -o json | jq -r '. | .[] | @base64d' | jq '.'
```

