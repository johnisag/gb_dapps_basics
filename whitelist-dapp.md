---
description: Create a whitelist dApp
---

# Whitelist dApp

> A whitelist (allowlist) is a cybersecurity strategy that **approves a list of email addresses, IP addresses, domain names or applications, while denying all others**

### Requirements

* Whitelist access should be given to the first `10` users for free who want to get in.
* There should be a website where people can go and enter into the whitelist.

### Build

#### Smart Contract

To build the smart contract we will be using [Hardhat](https://hardhat.org/).

```shell
mkdir Whitelist-Dapp
cd Whitelist-Dapp

mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat

npx hardhat
```

add contract **Whitelist.sol** in the **contracts** folder

<pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity">//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;


contract Whitelist {
    // Max number of whitelisted addresses allowed
    uint8 public maxWhitelistedAddresses;

    // Create a mapping of whitelistedAddresses
<strong>    // if an address is whitelisted, we would set it to true, it is false by default  
</strong><strong>    // for all other addresses.
</strong>    mapping(address => bool) public whitelistedAddresses;

    // numAddressesWhitelisted would be used to keep track of how many addresses have
    // been whitelisted
    // NOTE: Don't change this variable name, as it will be part of the verification
    uint8 public numAddressesWhitelisted;

    // Setting the Max number of whitelisted addresses
    // User will put the value at the time of deployment
    constructor(uint8 _maxWhitelistedAddresses) {
        maxWhitelistedAddresses =  _maxWhitelistedAddresses;
    }

    /**
        addAddressToWhitelist - This function adds the address of the sender to the
        whitelist
     */
    function addAddressToWhitelist() public {
        // check if the user has already been whitelisted
        require(!whitelistedAddresses[msg.sender], 
        "Sender has already been whitelisted");
        // check if the numAddressesWhitelisted &#x3C; maxWhitelistedAddresses, if not
        //then throw an error.
        require(numAddressesWhitelisted &#x3C; maxWhitelistedAddresses, 
        "More addresses cant be added, limit reached");
        // Add the address which called the function to the whitelistedAddress array
        whitelistedAddresses[msg.sender] = true;
        // Increase the number of whitelisted addresses
        numAddressesWhitelisted += 1;
    }
}</code></pre>

We will deploy the contract to the **`goerli`** network.

Create/replace ./**`scripts`**/**`deploy.js`**.

```solidity
const { ethers } = require("hardhat");

async function main() {
  /*
  A ContractFactory in ethers.js is an abstraction used to deploy new smart contracts,
  so whitelistContract here is a factory for instances of our Whitelist contract.
  */
  const whitelistContract = await ethers.getContractFactory("Whitelist");

  // here we deploy the contract
  const deployedWhitelistContract = await whitelistContract.deploy(10);
  // 10 is the Maximum number of whitelisted addresses allowed

  // Wait for it to finish deploying
  await deployedWhitelistContract.deployed();

  // print the address of the deployed contract
  console.log("Whitelist Contract Address:", deployedWhitelistContract.address);
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });// Some codeidi
```

Create a `.env` file in the `hardha_tutorial` folder and add the following lines

```python
QUICKNODE_HTTP_URL="add-quicknode-http-provider-url-here"
PRIVATE_KEY="add-the-private-key-here"
```

Go to [Quicknode](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) and sign up for an account.&#x20;

Quicknode is a node provider that lets you connect to various different blockchains. We will be using it to deploy our contract through Hardhat. After creating an account, `Create an endpoint` on Quicknode, select `Ethereum`, and then select the `Goerli` network. Click `Continue` in the bottom right and then click on `Create Endpoint`. Copy the link given to you in `HTTP Provider` and add it to the `.env` file below for `QUICKNODE_HTTP_URL`

To get your private key, you need to export it from Metamask. Open Metamask, click on the three dots, click on `Account Details` and then `Export Private Key`. MAKE SURE YOU ARE USING A TEST ACCOUNT THAT DOES NOT HAVE MAINNET FUNDS FOR THIS. Add this Private Key below in your `.env` file for `PRIVATE_KEY` variable.

install `dotenv` package to be able to import the env file and use it in our config

```shell
npm install dotenv
```

Update the hardhat.config.js

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

Compile through shell

```shell
npx hardhat compile
```

<mark style="color:orange;">**NOTE:**</mark> If we experience **Error: Cannot find module '@nomicfoundation/hardhat-toolbox'**, try to run the below to resolve dependency from shell

```shell
npm install --save-dev @nomicfoundation/hardhat-toolbox
```

Deploy through shell

```shell
npx hardhat run scripts/deploy.js --network goerli
```

Copy your deployed contract address from **shell** and find your new token contract on  [Goerli Etherscan  ](https://goerli.etherscan.io/)





