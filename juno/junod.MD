# Junod

**Table of Contents**

- ### Cheat Sheet
- ### Installation 
- ### Setting up junod config
- ### Keys
- ### Misc

---
---

# ~-~ Cheat Sheet ~-~

### Contracts

**Store Contract**
```bash
junod tx wasm store <MY_CONTRACT.wasm> --from <MY_WALLET> --gas-prices 0.
1ujunox --gas auto --gas-adjustment 1.3 -y -b block
```

</br>

**Instantiate Contract**
```bash
junod tx wasm instantiate 1234 '{}' --label "leturts_contract" --from <YOUR WALLET ADDRESS> --admin <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -b block -y
```

</br>

**Execute Contract (without funds)**
```bash
junod tx wasm execute <YOUR CONTRACT ADDRESS> '{"increment": {}}' --from <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -y -b block
```

</br>

**Execute Contract (with funds)**
```bash
junod tx wasm execute <YOUR CONTRACT ADDRESS> '{"increment": {}}' --amount 1000000ujunox --from <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -y -b block
```
- 1,000,000 ujuno == 1 juno

---
---

# Keys

**Show all keys**
```bash
junod keys list
```

---
---

# ~-~ Misc ~-~

### Query juno balance of an address
```bash
junod query bank balances juno1xxx...
```

</br>

### Query info about a cw-20 token

- Get the cw-20 token contract address from a [block explorer](https://www.mintscan.io/juno)

- Run
```bash
junod query wasm contract-state smart juno1xxx... '{"token_info": {}}'
```

- Output
```rust
data:
  decimals: 8
  name: Shipcoin
  symbol: SHIP
  total_supply: "1000000000000000"
```