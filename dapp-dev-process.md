---
description: The key process of developing dapps
---

# dApp Dev Process

### Requirements

* Download and Install [MetaMask](https://metamask.io/)
* Request some Goerli Tesnet Ether  [Faucet link to request funds](https://faucets.chain.link/)
* Install Node.js ([Download and Instructions](https://nodejs.org/en/download/))
* Install lite-server (with NPM in a terminal/command prompt)

```shell
# This installs `lite-server` globally (-g) on your computer
npm install -g lite-server
```

### Smart Contract Dev

**1) To build the smart contract we will be using** [**Hardhat**](https://hardhat.org/)**; for bootstraping.**

```shell
mkdir Whitelist-Dapp
cd Whitelist-Dapp

mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat

npx hardhat
```

**2) install** [**Open Zeppelin**](https://github.com/OpenZeppelin/openzeppelin-contracts) **contracts if required**

```shell
npm install @openzeppelin/contracts
```

**3) Add contract(s) in the ./contracts folder**

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;
contract MyContract {...}
```

**4) Prepare for deployment to the** [**`goerli` ** ](https://goerli.etherscan.io/)**test network using** [Quicknode](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) **endpoint.**

* **Sign in to**  [**Quicknode**](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) **and create a goerli endpoint**
* install [**`dotenv` ** ](https://www.npmjs.com/package/dotenv)package to be able to import the env file and use it in our config

```shell
npm install dotenv
```

* Create a ** `.env`** file in the [**`Hardhat`**](https://hardhat.org/)folder and add the following lines

```javascript
QUICKNODE_HTTP_URL="add-quicknode-http-provider-url-here"
PRIVATE_KEY="add-the-private-key-here"
```

**5) Put constants (if any) in a centralized place. Create ./constants/index.js**

```javascript
// Another contract address which is used in deploy (this is an example)
const ANOTHER_CONTRACT_ADDRESS = "0xA3b72e87664D52c3AcFCf4048C021E8931a3b759";

module.exports = { ANOTHER_CONTRACT_ADDRESS };
```

**6) Create/replace ./`scripts`/`deploy.js`.**

```javascript
const { ethers } = require("hardhat");
require("dotenv").config({ path: ".env" });
const { ANOTHER_CONTRACT_ADDRESS } = require("../constants");

async function main() {
  // another contract address which is used inour main contract
  const anotherContractAddress = ANOTHER_CONTRACT_ADDRESS;

  // ContractFactory in ethers.js is an abstraction used to deploy new smart contracts
  const myContract= await ethers.getContractFactory("MyContract");

  // here we deploy the contract
  // The constractor uses another contract address
  const deployedMyContract = await myContract.deploy(anotherContractAddress);

  // Wait for it to finish deploying
  await deployedWhitelistContract.deployed();

  // print the address of the deployed contract
  console.log("Contract Address:", deployedMyContract .address);
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

**7) Set hardhat to use** [**`goerli` ** ](https://goerli.etherscan.io/)**network using** [**Quicknode**](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) **endpoint from DEPLOYMENT**

* Update the hardhat.config.js

```javascript
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const QUICKNODE_HTTP_URL = process.env.QUICKNODE_HTTP_URL;
const PRIVATE_KEY = process.env.PRIVATE_KEY;

module.exports = {
  solidity: "0.8.9",
  networks: {
    goerli: {
      url: QUICKNODE_HTTP_URL,
      accounts: [PRIVATE_KEY],
    },
  },
};
```

**8) Compile and Deploy from** [**Hardhat** ](https://hardhat.org/)**root folder**

```shell
# compile
npx hardhat compile

# If we experience Error: Cannot find module '@nomicfoundation/hardhat-toolbox', then
npm install --save-dev @nomicfoundation/hardhat-toolbox

# deploy to goerli
npx hardhat run scripts/deploy.js --network goerli
```

### dApp Dev

**1) Boostrap the**  [**React**](https://reactjs.org/) **/** [**Next Js**](https://nextjs.org/) **web app (from project root)**

```shell
npx create-next-app@latest

# start local web server on http://localhost:3000; see that bootstrap worked
cd my-app
npm run dev
```

**2) Install required web3 libraries (** [Web3Modal library](https://github.com/Web3Modal/web3modal), [ethers](https://docs.ethers.io/v3/)**)**

```shell
npm install web3modal
npm install ethers
```

****

### dApp Deploy - [Vercel](https://vercel.com/)&#x20;

* Go to [Vercel](https://vercel.com/) and sign in with your GitHub
* Then click on `New Project` button and then select your project repo
* When configuring your new project, Vercel will allow you to customize your `Root Directory`
* Click `Edit` next to `Root Directory` and set it to `my-app`
* Select the Framework as `Next.js`
* Click `Deploy`

### Misc
