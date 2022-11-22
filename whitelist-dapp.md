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
