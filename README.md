# Structure of the repository

The four directories contain the following:

1. `starknet/`: lists of eligibles (identified by their Starknet address) with allocations and Merkle paths, for the categories:
    - Starknet users
    - Early community members (ECMP recipients)
2. `eth/`: lists of eligibles (identified by their Ethereum address) with allocations and Merkle paths, for the categories:
    - Ethereum stakers
    - Protocol core guild
    - dYdX users
    - ImmutableX users
3. `github/`: lists of eligibles (identified by their GitHub username) and allocations for the categories:
    - Starknet developers
    - EIP authors
    - Ethereum developers
    - General developers
4. `stark_key/`: lists of eligibles (identified by their STARK public key) with allocations and Merkle paths, for the categories:
    - Sorare users
    - rhino.fi users

## GitHub squatted usernames

After the Provisions announcement on February 14th 2024, a total of 1796 GitHub eligible handles that were free at the time of the announcement have been squatted since. Those usernames will not be able to claim Provisions. The username list is in `github_squatted_usernames.csv`.

# Claiming STRK outside the Provisions portal

## Ethereum eligibles

To claim your allocation outside the web portal your Ethereum address must be among the list of eligibles in the directory `eth/`. To claim, follow these steps:

1. Download a Starknet wallet ([Argent](https://www.argent.xyz/) or [Braavos](https://braavos.app/)) and initialize it. At this point you should have a valid Starknet address (a 252-bit number encoded as an hex string).
2. Find the Merkle path and the Merkle index corresponding to your identity, from the lists in the directory `eth/`.

The next step is sending an Ethereum transaction to Starknet’s core contract on mainnet (at address `0xc662c410C0ECf747543f5bA90660f6ABeBD9C8c4`), calling the function `sendMessageToL2` (https://etherscan.io/address/0xc662c410c0ecf747543f5ba90660f6abebd9c8c4#writeProxyContract#F6).

The function `sendMessageToL2` needs to be called with appropriate `msg.value` (i.e. transferred ETH) and with the appropriate calldata. In the above Etherscan link, the first parameter is the payable amount (i.e. the `msg.value`) and the other 3 parameters are the inputs to the function `sendMessageToL2`.

The latter three parameters are as follows:

1. **toAddress**: `0x071808540ed1139bcc8bb55eb975e8168758f2a342ce3f22c512a1c8da1b84dc`
2. **selector**: `0x00828430c65c40cba334d4723a4c5c02a62f612d73d564a1c7dc146f1d0053f9`
3. **payload**: [`eth_address`, `balance`, `0`, `index`, `len_merkle_path`, `merkle_path`, `starknet_address`], where
    1. `eth_address`: your eth address.
    2. `balance`: the amount allocated to you, measured in fri units (1 STRK = $10^{18}$ fri).
    3. `merkle_index`: See Step 2 at the beginning.
    4. `len_merkle_path`: length of the array merkle_path.
    5. `merkle_path`: See Step 2 at the beginning.
    6. `starknet_address`: the address that you obtained in Step 1 at the beginning.

The transaction must have a correct payable amount: the invoked function costs roughly 3000 gas, so you should set `msg.value` around 3000*`gas_price`.


### Example

Here is an example using Etherscan's interface:

![alt text](etherscan.png)

Note that the large integers in the calldata need to be passed as strings in Etherscan.
The above is a correctly filled tx corresponding to:
- eth address = `0x1234567890123456789012345678901234567890`.
- allocation amount of `800` STRK.
- Merkle index = `17`.
- Merkle path = [`0x4321`, `0x1234`].
- Starknet recipient address = `0x1010101010101010101010101010101010101010101010101010101010101010`.


## Starknet eligibles

You’re in this category if your Starknet address is among the list of eligibles in the directory `starknet/`.

To claim your allocation outside the web portal:

1. Find the Merkle path and the Merkle index corresponding to your identity, from the lists in the directory `starknet/`.
2. Send a tx to the Starknet provisions contract (at address `0x06793d9e6ed7182978454c79270e5b14d2655204ba6565ce9b0aa8a3c3121025`), invoking the function `claim` with inputs:
    - `identity`: felt
    - `amount`: u256
    - `index`: u128
    - `merkle_path`: span of felts

The easiest way to do this is as follows: go to the Starknet provision's contract on one of Starknet's block explorers and find the `claim` function of the contract. [Here](https://starkscan.co/contract/0x06793d9e6ed7182978454c79270e5b14d2655204ba6565ce9b0aa8a3c3121025#read-write-contract-sub-write) is a link to the contract's functions on Starkscan: once you click on the function `claim` you will see the following:

![alt text](starkscan.png)

First, connect your wallet by clicking the blue button "Connect to Wallet" in the upper left (you can use any wallet address of your choosing).

Then fill in the blank - indicated by the red arrow in the above image - with the following data, separated by commas:

```
your eligible Starknet address, your STRK allocation in fri units, 0, merkle_index, length of merkle_path, 1st felt of merkle_path, ... , N-th felt of merkle_path
```

For example, if 
- your eligible Starknet address is `0x1010101010101010101010101010101010101010101010101010101010101010`,
- with allocation of `200` STRK (which equals $200*10^{18}$ fri),
- Merkle index = `9`,
- Merkle path = [`0x1234`, `0x4321`],

then the blank will be filled with
`0x1010101010101010101010101010101010101010101010101010101010101010`, `200000000000000000000`, `0`, `9`, `2`, `0x1234`, `0x4321`.

To send the transaction, click the button "Write". You will be prompted by your wallet to allow the transaction. 
