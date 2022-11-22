---
description: What is Gas and why is need ?
---

# Gas

Just like how **`seconds` ** are a unit of time, and **`metres` ** a unit of distance, **`gas` ** by itself is a unit of computation on the Ethereum Network.

A recent upgrade, the London Upgrade of August 2021, slightly changed how transaction fees are calculated and how gas works. For that reason, we will break this tutorial into two sections:

* Pre-London Upgrade
* Post-London Upgrade

The **gas unit** is used to measure the amount of computational effort required to execute a transaction on Ethereum. Since each transaction requires some computation resources to execute, it requires a fee, commonly called **Gas fees** or **Transaction fees**.

### Pre-London Upgrade

`gas fees = gas spent * gas price`

* **Gas Spent** is the total amount of gas (in gas units) that were used to execute the transaction
* **Gas Price** is the amount of ether you're willing to pay per gas unit of execution

Gas prices are denominated in **gwei** - a denomination of ETH.

**1 Gwei = 0.000000001 ETH**

**1 ETH = 10^9 Gwei**

#### Example

The cheapest transaction, in terms of amount of gas required to execute, is just the transfer of ETH from one account to another. This transaction costs 21,000 gas units.

Suppose Alice wanted to pay Bob 1 ETH. The gas cost is 21,000 gas. Assume the gas price is 200 Gwei.

Therefore, `gas fees = 21,000 * 200 = 4,200,000 Gwei = 0.0042 ETH`

#### Gas Cost Calculation

When a smart contract is compiled into bytecode, before deployment to the Ethereum network, it is compiled down to OPCODES.

Each OPCODE has a fixed gas cost. The gas cost of a specific function within the smart contract is the sum of the gas costs of all it's OPCODES. [You can find a list of all OPCODES and their associated gas costs here if interested.](https://github.com/crytic/evm-opcodes)

#### Gas Limits

**Gas Limit** refers to the maximum amount of gas (units) you're willing to use for the transaction. This is set by the user.

**If, however, your transaction uses more gas than your limit, the transaction will fail and your gas will be gone.**

#### Block Gas Limits

In addition to user specified gas limits per transaction, the Ethereum network also imposes a limit on the maximum amount of gas (units) allowed in a single block.

This is done to ensure that each block stays within an allowable range of computational cost. Since more complex transactions take longer to execute, this makes sure that nodes don't start falling out of sync with the rest of the network due to increased computational complexity.

### Post-London Upgrade

* Better gas fees estimations
* Quicker transaction inclusion
* Burning a percentage of ETH being used as transaction fees

Prior to the London Upgrade, wallets like Metamask would provide estimates for gas prices based on past network activity. Every wallet used their own methodology to do so. Metamask, specifically, scanned the last 1000 blocks on Ethereum, and predicted the gas price for your transaction.

Starting with the London Upgrade however, every block was set to have a **base gas price fees**. This was the **minimum** price per unit of gas for getting your transaction included within this block. This was calculated natively by the network based on the demand for block space. These base fees would go on to be burnt by the Ethereum network, therefore forever getting rid of that ETH to offset issuance. Since Ethereum does not have an overall maximum supply (unlike Bitcoin, which has a maximum supply of 21M Bitcoins), the burn helps the ETH supply reach an equilibrium by not inflating it infinitely.

gas fees = gas spent \* (base fees + priority fees)

#### Example

Going back to the earlier example, if Alice had to pay Bob 1 ETH, the gas cost (in units) is 21,000. Suppose the base fees is 100 Gwei, and Alice decides to include a tip of 10 Gwei.

`total gas fees = 21,000 * (100 Gwei + 10 Gwei) = 2,310,000 Gwei = 0.00231 ETH`

#### Variable Block Sizes

Prior to the London Upgrade, the block gas limit was constant for all blocks. Each block had a maximum capacity of 15M gas.&#x20;

The upgrade introduced variable size blocks to Ethereum. Each block now has a **target** gas limit of **15M gas**, but the size can increase or decrease along with network demand, up until a maximum of **30M gas**.

**If the block gas is greater than the 15M target, the base fees for the next block is increased. Similarly, if the block gas is smaller than the 15M target, the base fees for the next block is decreased.**&#x20;

#### Variable Base Fees

The base fees is increased by a maximum of 12.5% per block if the target 15M gas is exceeded. This exponential growth makes it financially non-viable for block gas to remain high indefinitely, therefore allowing nodes to stay synced with the network and not constantly be executing 30M gas blocks.

![](<.gitbook/assets/image (4).png>)

#### Better Gas Estimation

Wallets now know a minimum and maximum range of base fees to provide to the user when supplying estimations. The minimum is the **`current base fees * 87.5%`,** and the maximum is the ** `current base fees * 112.5%`** The user can then just adjust the tip, which is usually a fraction of the base fees, for the miner.

### Why does Gas exist?

Gas fees help keep the Ethereum network secure. By requiring a fee for every computation executed on the network, bad actors are prevented from spamming the network.

In order to avoid accidental or malicious infinite loops in smart contracts, which would cause all Ethereum nodes to forever be stuck, gas limits on transactions set a limit to how much computation a transaction can use.

Code like this will use up all the gas provided, upto the limit, and then the transaction will fail:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract Gas {
    uint public i = 0;

    // Using up all of the gas that you send causes your transaction to fail.
    // State changes are undone.
    // Gas spent are not refunded.
    function forever() public {
        // Here we run a loop until all of the gas are spent
        // and the transaction fails
        while (true) {
            i += 1;
        }
    }
}
```

### Reducing Gas Fees

High fees on Ethereum is a hot topic these days. The Ethereum community has solely sworn to not hurt the decentralization or security of the network. As such, tradeoffs were made in favour of security which leads to the Ethereum network currently having higher transaction fees than other blockchains such as Solana, which made the tradeoff in favour of lower fees at the expense of security and decentralization.

Ethereum's fundamental goal is to be a **highly secure** and **highly decentralized** blockchain network capable of executing smart contracts.

As such, there are a lot of things being worked on, and some already available, to allow for a reduction in gas fees and improve the user experience.

Primarily, the network upgrades that will be offered by Ethereum 2.0 (also known as Eth2) will ultimately address some of the gas issues, which in turn will enable the network to process thousands of transactions per second and scale globally.

**In addition, a lot of work is being done on Layer 2 Scaling.** Essentially they are networks which shift the heavy computation aspects of smart contracts somewhere else, and use the Ethereum mainnet as a final settlement layer, thereby inheriting the security and decentralization benefits of Ethereum, and maintaining low gas fees and high transaction speed for users.
