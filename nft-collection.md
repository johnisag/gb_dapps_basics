# NFT Collection

### Requirements

* There should only exist 20 Crypto Dev NFT's and each one of them should be unique.
* User's should be able to mint only 1 NFT with one transaction.
* Whitelisted users, should have a 5 min presale period before the actual sale where they are guaranteed 1 NFT per transaction.
* There should be a website for your NFT Collection.

### Basics

* NFTs are unique; each one is different. Every single token has unique characteristics and values. They are all distinguishable from one another and are not interchangeable eg Unique Art
* &#x20;ERC-721 is an open standard that describes how to build Non-Fungible tokens on EVM (Ethereum Virtual Machine) compatible blockchains; it is a standard interface for Non-Fungible tokens; it has a set of rules which make it easy to work with NFTs. [ERC721](https://docs.openzeppelin.com/contracts/3.x/api/token/erc721)

### Smart Contract

We will be using [**`Ownable.sol`**](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) from Openzeppelin to manage the **`Ownership`** of a contract

* By default, the owner of an **Ownable** contract is the account that deployed it, which is usually exactly what you want.
* **Ownable** also **allows** you to:
  * **transferOwnership** from the **owner account to a new one**, and
  * **renounceOwnership** for the owner to relinquish this administrative privilege, a common pattern after an initial stage with centralized administration is over.

We will also be using an extension of ERC721 known as [**ERC721 Enumerable**](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol)

* **ERC721 Enumerable** helps you to **keep track of all the tokenIds** in the contract **and** also the **tokensIds held by an address** for a given contract.
* Please have a look at the [**functions**](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#ERC721Enumerable) it implements before moving ahead

To build the smart contract we would be using [**Hardhat**](https://hardhat.org/).

* Hardhat is an **Ethereum development environment and framework** designed for full stack development in Solidity.
* You can **write** your smart contract, **deploy** them, **run tests**, and **debug** your code.

**Setup a Hardhat project**

```shell
mkdir NFT-Collection
cd NFT-Collection
mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat

# bootstrap the hardhat project
npx hardhat
```

Make sure you select **`Create a Javascript Project`** and then follow the steps in the terminal to complete your Hardhat setup.

Install **`@openzeppelin/contracts`** as we would be importing [**Openzeppelin's ERC721Enumerable Contract**](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol)&#x20;

```shell
npm install @openzeppelin/contracts
```

We will need to call the **`Whitelist Contract`** that you deployed for your previous level to check for addresses that were whitelisted and give them presale access. We only need to call **`mapping(address => bool) public whitelistedAddresses;`**&#x20;

We can create an interface for **`Whitelist contract`** with a function only for this mapping, this way we would save **`gas`** as we would not need to inherit and deploy the entire **`Whitelist Contract`** but only a part of it.

Create a new file inside the **`contracts`** directory and call it **`IWhitelist.sol`**.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

interface IWhitelist {
    function whitelistedAddresses(address) external view returns (bool);
}
```

