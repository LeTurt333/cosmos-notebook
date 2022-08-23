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

**Instantiate Contract**
```bash
junod tx wasm instantiate 1234 '{}' --label "leturts_contract" --from <YOUR WALLET ADDRESS> --admin <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -b block -y
```

**Execute Contract (without funds)**
```bash
junod tx wasm execute <YOUR CONTRACT ADDRESS> '{"increment": {}}' --from <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -y -b block
```

**Execute Contract (with funds)**
```bash
junod tx wasm execute <YOUR CONTRACT ADDRESS> '{"increment": {}}' --amount 1000000ujunox --from <YOUR WALLET ADDRESS> --gas auto --gas-prices 0.1ujunox --gas-adjustment 1.3 -y -b block
```
- 1,000,000 ujuno == 1 juno

</br>

### Keys

**Show all keys**
```bash
junod keys list
```


</br>

---

</br>



# ~-~ Setting up junod config ~-~

To Do


</br>

---




# ~-~ Keys ~-~

To Do

</br>

---




# ~-~ Misc ~-~

### Query juno balance of an address
```bash
junod query bank balances juno1xxx...
```

---

### Query info about a cw-20 token

- Get the cw-20 token contract address from a block explorer
    - [Mintscan](https://www.mintscan.io/juno)


- Command
```bash
junod query wasm contract-state smart juno1xxx... '{"token_info": {}}'
```

- Output
```json
data:
  decimals: 8
  name: Shipcoin
  symbol: SHIP
  total_supply: "1000000000000000"
```