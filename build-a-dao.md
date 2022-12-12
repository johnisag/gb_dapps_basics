---
description: Build a dao for our NFT collection
---

# Build a DAO

###

### Basics

#### What is a DAO?

* DAO stands for **D**ecentralized **A**utonomous **O**rganization
* You can think of DAOs as analogous to companies in the real world
* DAOs allow for members to create and vote on governance decisions
* Membership in DAOs is typically restricted either by ownership of ERC20 tokens, or by ownership of NFTs
* Examples of DAOs where they are based on NFTs include [Meebits DAO](https://www.meebitsdao.world/).
* Examples of DAOs where membership and voting power is proportional to how many tokens you own include [Uniswap](https://uniswap.org/) and [ENS](https://ens.domains/).

In traditional companies, when a decision needs to be made, the board of directors or executives of the company are in charge of making that decision. In a DAO, this process is democratized, and any member can create a proposal, and all other members can vote on it. Each proposal created has a deadline for voting, and after the deadline the decision is made in favour of the voting outcome (YES or NO).

### Building our DAO

* We want to launch a DAO for holders of our **`CryptoDevs`** NFTs.
* We built up a DAO Treasury from the ETH that was gained through the ICO
* The DAO now has a lot of ETH, but currently does nothing with it.
* We want to allow our NFT holders to **create** and **vote on proposals** to use that ETH for **purchasing other NFTs** from an NFT marketplace, and **speculate on price**
* Maybe in the future when we sell the NFT back, you split the profits among all members of the DAO.

### Requirements

* Anyone with a `CryptoDevs` NFT can create a proposal to purchase a different NFT from an NFT marketplace
* Everyone with a `CryptoDevs` NFT can vote for or against the active proposals
* Each NFT counts as one vote for each proposal
* Voter cannot vote multiple times on the same proposal with the same NFT
* If majority of the voters vote for the proposal by the deadline, the NFT purchase is automatically executed

### What we will make

* To be able to purchase NFTs automatically when a proposal is passed, you need an on-chain NFT marketplace that you can call a **`purchase()`** function on
* We will **create a simplified fake NFT marketplace** for this tutorial as the **focus is on the DAO.**
* We will also **make** the **actual DAO smart contract** using **Hardhat**.
* We will **make** the **website using Next.js** to allow users to **create and vote on proposals**

### Smart Contract

**Setup a Hardhat project**

```shell
mkdir dao
cd dao
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

Create**`FakeNFTMarketplace.sol`**  inside **./contracts**

```
// Some code
```

Create**`CryptoDevsDAO.sol` ** inside **./contracts**

```
// Some code
```
