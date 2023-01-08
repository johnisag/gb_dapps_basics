---
description: Build your own Decentralized Exchange like Uniswap
---

# Build a DEX

### Requirements

* Build an exchange with only one asset pair (Eth / Crypto Dev)
* Your Decentralized Exchange should take a fees of `1%` on swaps
* When user adds liquidity, they should be given `Crypto Dev LP` tokens (Liquidity Provider tokens)
* CD LP tokens should be given proportional to the `Ether` user is willing to add to the liquidity

### Smart Contract

**Setup a Hardhat project**

```shell
mkdir dex_tutorial
cd dex_tutorial
mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat

# bootstrap the hardhat project
npx hardhat
```

Install **`@openzeppelin/contracts`** as we would be importing [**Openzeppelin's ERC20 Contract** ](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol)

```shell
npm install @openzeppelin/contracts
```

Create**`Exchange.sol`**  inside **./contracts**

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Exchange is ERC20 {

}
```
