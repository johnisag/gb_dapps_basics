---
description: The key process of developing dapps
---

# The Process

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

**2) Add contract(s) in the contracts folder**

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;
contract myContract {...}
```

**3) Prepare for deployment to the `goerli` network using** [Quicknode](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) **endpoint.**

* **Sign in to**  [**Quicknode**](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) **and create a goerli endpoint**
* install **`dotenv` ** package to be able to import the env file and use it in our config

```shell
npm install dotenv
```

* Create a ** `.env`** file in the **`hardhat`**folder and add the following lines

```javascript
QUICKNODE_HTTP_URL="add-quicknode-http-provider-url-here"
PRIVATE_KEY="add-the-private-key-here"
```

**4) Create/replace ./`scripts`/`deploy.js`.**

```solidity
const { ethers } = require("hardhat");

async function main() {
  // ContractFactory in ethers.js is an abstraction used to deploy new smart contracts
  const myContract= await ethers.getContractFactory("myContract");

  // here we deploy the contract
  const deployedMyContract = await myContract.deploy(10);
  // 10 is the Maximum number of addresses allowed

  // Wait for it to finish deploying
  await deployedWhitelistContract.deployed();

  // print the address of the deployed contract
  console.log("Contract Address:", deployedWhitelistContract.address);
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

**5) Set hardhat to use `goerli` network using** [**Quicknode**](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) **endpoint from DEPLOYMENT**

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

**6) Compile and Deploy from Hardhat root folder**

```shell
# compile
npx hardhat compile

# If we experience Error: Cannot find module '@nomicfoundation/hardhat-toolbox', then
npm install --save-dev @nomicfoundation/hardhat-toolbox

# deploy to goerli
npx hardhat run scripts/deploy.js --network goerli
```

### dApp Dev

TODO&#x20;

### dApp Deploy - [Vercel](https://vercel.com/)&#x20;

* Go to [Vercel](https://vercel.com/) and sign in with your GitHub
* Then click on `New Project` button and then select your project repo
* When configuring your new project, Vercel will allow you to customize your `Root Directory`
* Click `Edit` next to `Root Directory` and set it to `my-app`
* Select the Framework as `Next.js`
* Click `Deploy`

### Misc
