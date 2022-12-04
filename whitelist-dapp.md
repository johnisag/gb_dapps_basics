---
description: Create a whitelist dApp
---

# Whitelist dApp

> A whitelist (allowlist) is a cybersecurity strategy that **approves a list of email addresses, IP addresses, domain names or applications, while denying all others**

### Requirements

* Whitelist access should be given to the first `10` users for free who want to get in.
* There should be a website where people can go and enter into the whitelist.

### Smart Contract

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
}
</code></pre>

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

### Website

To develop the website we will use [React](https://reactjs.org/) and [Next Js](https://nextjs.org/).

Create the new **`Next.js`** app from shell (project root)

```shell
npx create-next-app@latest
```

The folder structure should look something like

```
- Whitelist-Dapp    
    - hardhat-tutorial    
    - my-app
```

Now to run the app, execute these commands in the terminal

```shell
cd my-appnpm 
run dev
```

Now go to `http://localhost:3000`, your app should be running 🤘

Now let's install [Web3Modal library](https://github.com/Web3Modal/web3modal).&#x20;

* Web3Modal is an easy-to-use library to help developers easily allow their users to connect to your dApps with all sorts of different wallets.&#x20;
* By default Web3Modal Library supports injected providers like (Metamask, Dapper, Gnosis Safe, Frame, Web3 Browsers, etc) and WalletConnect,&#x20;
* You can also easily configure the library to support Portis, Fortmatic, Squarelink, Torus, Authereum, D'CENT Wallet and Arkane. (Here's a live example on [Codesandbox.io](https://codesandbox.io/s/j43b10))

Open up a terminal pointing **at`my-app` directory** and execute this command

```shell
npm install web3modal
npm install ethers
```

Download [**this image**](https://github.com/LearnWeb3DAO/Whitelist-Dapp/blob/main/my-app/public/crypto-devs.svg) **** in **my-app/public folder** and rename it to **`crypto-devs.svg`**&#x20;

Go to **`styles`** folder and replace all the contents of **`Home.modules.css`** file with the following code

```css
.main {
  min-height: 90vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-family: "Courier New", Courier, monospace;
}

.footer {
  display: flex;
  padding: 2rem 0;
  border-top: 1px solid #eaeaea;
  justify-content: center;
  align-items: center;
}

.image {
  width: 70%;
  height: 50%;
  margin-left: 20%;
}

.title {
  font-size: 2rem;
  margin: 2rem 0;
}

.description {
  line-height: 1;
  margin: 2rem 0;
  font-size: 1.2rem;
}

.button {
  border-radius: 4px;
  background-color: blue;
  border: none;
  color: #ffffff;
  font-size: 15px;
  padding: 20px;
  width: 200px;
  cursor: pointer;
  margin-bottom: 2%;
}
@media (max-width: 1000px) {
  .main {
    width: 100%;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
}
```

Open the **index.js** file under **the pages folder** and **paste** the following code

{% code overflow="wrap" %}
```javascript
import Head from "next/head";
import styles from "../styles/Home.module.css";
import Web3Modal from "web3modal";
import { providers, Contract } from "ethers";
import { useEffect, useRef, useState } from "react";
import { WHITELIST_CONTRACT_ADDRESS, abi } from "../constants";

export default function Home() {
  // walletConnected keep track of whether the user's wallet is connected or not
  const [walletConnected, setWalletConnected] = useState(false);
  // joinedWhitelist keeps track of whether the current metamask address has joined
  // the Whitelist or not
  const [joinedWhitelist, setJoinedWhitelist] = useState(false);
  // loading is set to true when we are waiting for a transaction to get mined
  const [loading, setLoading] = useState(false);
  // numberOfWhitelisted tracks the number of addresses's whitelisted
  const [numberOfWhitelisted, setNumberOfWhitelisted] = useState(0);
  // Create a reference to the Web3 Modal (used for connecting to Metamask) which
  // persists as long as the page is open
  const web3ModalRef = useRef();

  /**
   * Returns a Provider or Signer object representing the Ethereum RPC with or without the
   * signing capabilities of metamask attached
   *
   * A `Provider` is needed to interact with the blockchain - reading transactions, reading balances, reading state, etc.
   *
   * A `Signer` is a special type of Provider used in case a `write` transaction needs to be made to the blockchain, which involves the connected account
   * needing to make a digital signature to authorize the transaction being sent. Metamask exposes a Signer API to allow your website to
   * request signatures from the user using Signer functions.
   *
   * @param {*} needSigner - True if you need the signer, default false otherwise
   */
  const getProviderOrSigner = async (needSigner = false) => {
    // Connect to Metamask
    // Since we store `web3Modal` as a reference, we need to access the `current` 
    // value to get access to the underlying object
    const provider = await web3ModalRef.current.connect();
    const web3Provider = new providers.Web3Provider(provider);

    // If user is not connected to the Goerli network, let them know and throw an 
    // error
    const { chainId } = await web3Provider.getNetwork();
    if (chainId !== 5) {
      window.alert("Change the network to Goerli");
      throw new Error("Change network to Goerli");
    }

    if (needSigner) {
      const signer = web3Provider.getSigner();
      return signer;
    }
    return web3Provider;
  };

  /**
   * addAddressToWhitelist: Adds the current connected address to the whitelist
   */
  const addAddressToWhitelist = async () => {
    try {
      // We need a Signer here since this is a 'write' transaction.
      const signer = await getProviderOrSigner(true);
      // Create a new instance of the Contract with a Signer, which allows
      // update methods
      const whitelistContract = new Contract(
        WHITELIST_CONTRACT_ADDRESS,
        abi,
        signer
      );
      // call the addAddressToWhitelist from the contract
      const tx = await whitelistContract.addAddressToWhitelist();
      setLoading(true);
      // wait for the transaction to get mined
      await tx.wait();
      setLoading(false);
      // get the updated number of addresses in the whitelist
      await getNumberOfWhitelisted();
      setJoinedWhitelist(true);
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * getNumberOfWhitelisted:  gets the number of whitelisted addresses
   */
  const getNumberOfWhitelisted = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the 
      // blockchain
      const provider = await getProviderOrSigner();
      // We connect to the Contract using a Provider, so we will only
      // have read-only access to the Contract
      const whitelistContract = new Contract(
        WHITELIST_CONTRACT_ADDRESS,
        abi,
        provider
      );
      // call the numAddressesWhitelisted from the contract
      const _numberOfWhitelisted =
        await whitelistContract.numAddressesWhitelisted();
      setNumberOfWhitelisted(_numberOfWhitelisted);
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * checkIfAddressInWhitelist: Checks if the address is in whitelist
   */
  const checkIfAddressInWhitelist = async () => {
    try {
      // We will need the signer later to get the user's address
      // Even though it is a read transaction, since Signers are just special kinds
      // of Providers,
      // We can use it in it's place
      const signer = await getProviderOrSigner(true);
      const whitelistContract = new Contract(
        WHITELIST_CONTRACT_ADDRESS,
        abi,
        signer
      );
      // Get the address associated to the signer which is connected to  MetaMask
      const address = await signer.getAddress();
      // call the whitelistedAddresses from the contract
      const _joinedWhitelist = await whitelistContract.whitelistedAddresses(
        address
      );
      setJoinedWhitelist(_joinedWhitelist);
    } catch (err) {
      console.error(err);
    }
  };

  /*
    connectWallet: Connects the MetaMask wallet
  */
  const connectWallet = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // When used for the first time, it prompts the user to connect their wallet
      await getProviderOrSigner();
      setWalletConnected(true);

      checkIfAddressInWhitelist();
      getNumberOfWhitelisted();
    } catch (err) {
      console.error(err);
    }
  };

  /*
    renderButton: Returns a button based on the state of the dapp
  */
  const renderButton = () => {
    if (walletConnected) {
      if (joinedWhitelist) {
        return (
          <div className={styles.description}>
            Thanks for joining the Whitelist!
          </div>
        );
      } else if (loading) {
        return <button className={styles.button}>Loading...</button>;
      } else {
        return (
          <button onClick={addAddressToWhitelist} className={styles.button}>
            Join the Whitelist
          </button>
        );
      }
    } else {
      return (
        <button onClick={connectWallet} className={styles.button}>
          Connect your wallet
        </button>
      );
    }
  };

  // useEffects are used to react to changes in state of the website
  // The array at the end of function call represents what state changes will trigger
  // this effect
  // In this case, whenever the value of `walletConnected` changes - this effect will
  // be called
  useEffect(() => {
    // if wallet is not connected, create a new instance of Web3Modal and connect the
    // MetaMask wallet
    if (!walletConnected) {
      // Assign the Web3Modal class to the reference object by setting it's `current`
      // value
      // The `current` value is persisted throughout as long as this page is open
      web3ModalRef.current = new Web3Modal({
        network: "goerli",
        providerOptions: {},
        disableInjectedProvider: false,
      });
      connectWallet();
    }
  }, [walletConnected]);

  return (
    <div>
      <Head>
        <title>Whitelist Dapp</title>
        <meta name="description" content="Whitelist-Dapp" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className={styles.main}>
        <div>
          <h1 className={styles.title}>Welcome to Crypto Devs!</h1>
          <div className={styles.description}>
            Its an NFT collection for developers in Crypto.
          </div>
          <div className={styles.description}>
            {numberOfWhitelisted} have already joined the Whitelist
          </div>
          {renderButton()}
        </div>
        <div>
          <img className={styles.image} src="./crypto-devs.svg" />
        </div>
      </div>

      <footer className={styles.footer}>
        Made with &#10084; by Crypto Devs
      </footer>
    </div>
  );
}
```
{% endcode %}

### Deploy our dApp

1. Go to [Vercel](https://vercel.com/) and sign in with your GitHub
2. Then click on `New Project` button and then select your whitelist repo
3. When configuring your new project, Vercel will allow you to customize your `Root Directory`
4. Click `Edit` next to `Root Directory` and set it to `my-app`
5. Select the Framework as `Next.js`
6. Click `Deploy`















