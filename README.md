## Abstract
AMM working with the ratio of 2 fungible tokens

The user can transfer a number of tokens A to the AMM contract and in return receive a certain number of tokens B (similarly in the other direction).The contract supports a certain ratio of tokens A and B. $X * Y = K$ ( $K$ is some constant value, $X$ and $Y$ are the number of tokens A and B respectively)

## Create Accounts

```bash
owner_id=whatever.testnet
a_id=a.$owner_id
b_id=b.$owner_id
amm_id=amm.$owner_id
sim_id=sim.$owner_id

near="near --nodeUrl https://rpc.testnet.near.org"

$near login
$near create-account $a_id --masterAccount $owner_id
$near create-account $b_id --masterAccount $owner_id
$near create-account $amm_id --masterAccount $owner_id
$near create-account $sim_id --masterAccount $owner_id
```
Create the accounts using NEAR CLI. 

## Deploy and Initialize
```bash
near deploy $a_id --wasmFile="./token_contract.wasm"
near deploy $b_id --wasmFile="./token_contract.wasm"
near deploy $amm_id --wasmFile="./amm_contract.wasm"

near call $a_id new '{"owner_id":"'$owner_id'", "name":"A Token Contract", "symbol":"A", "total_supply":1000000000000, "decimals": 18}' --accountId=$owner_id
near call $b_id new '{"owner_id":"'$owner_id'", "name":"B Token Contract", "symbol":"B", "total_supply":20000000000000, "decimals": 15}' --accountId=$owner_id
near call $amm_id new '{"owner_id":"'$owner_id'", "a_contract_id":"'$a_id'", "b_contract_id":"'$b_id'"}' --accountId=$owner_id --gas=55000000000000
```
Use `near call` command to initialize them.

## Test AMM Functionality
```base
sim_id=b.whatever.testnet

near call $a_id storage_deposit '{"account_id": "'$sim_id'"}' --accountId=$owner_id --deposit=1
near call $a_id ft_transfer '{"receiver_id": "'$sim_id'","amount":"1000000000000000000000"}' --accountId=$owner_id --deposit=0.000000000000000000000001
near view $a_id ft_balance_of '{"account_id": "'$sim_id'"}'
```
First we use `b.whatever.testnet` account to simulate an AMM user. We register a A wallet for him and give him 1,000 tokens.
```bash
near call $amm_id deposit_a '{"amount":111}' --accountId=$sim_id --gas=55000000000000
```
```bash
near view $a_id ft_balance_of '{"account_id": "'$sim_id'"}'
near view $b_id ft_balance_of '{"account_id": "'$sim_id'"}'
near view $a_id ft_balance_of '{"account_id": "'$amm_id'"}'
near view $b_id ft_balance_of '{"account_id": "'$amm_id'"}'
near view $amm_id get_info
near view $amm_id get_ratio
```



