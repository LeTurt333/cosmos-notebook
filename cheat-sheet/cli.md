# Cheat Sheet - CLI <Juno>

### Optimized compile -> Upload -> Instantiate

***Optimized compile - Single contract***
```bash
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.6
```

***Optimized compile - Contract workspace***
```bash
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/workspace-optimizer:0.12.8
```

***Upload***
```bash
junod tx wasm store <MY_CONTRACT.wasm> --from <MY_WALLET> --gas auto --gas-prices 0.
1ujunox --gas-adjustment 1.3 -y -b block
```

***Instantiate***
```bash
junod tx wasm instantiate 1234 '{}' --label "leturts_contract" --from <YOUR WALLET ADDRESS> --admin <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -y -b block
```

