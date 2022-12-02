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

Create a new file inside the **/`contracts`** directory. **`CryptoDevs.sol:`**

<pre class="language-solidity" data-overflow="wrap"><code class="lang-solidity">// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "./IWhitelist.sol";

contract CryptoDevs is ERC721Enumerable, Ownable {
    /**
      * @dev _baseTokenURI for computing {tokenURI}. If set, the resulting URI for 
      * each
      * token will be the concatenation of the `baseURI` and the `tokenId`.
      */
    string _baseTokenURI;

    //  _price is the price of one Crypto Dev NFT
    uint256 public _price = 0.01 ether;

    // _paused is used to pause the contract in case of an emergency
    bool public _paused;

    // max number of CryptoDevs
    uint256 public maxTokenIds = 20;

    // total number of tokenIds minted
    uint256 public tokenIds;

    // Whitelist contract instance
    IWhitelist whitelist;

    // boolean to keep track of whether presale started or not
    bool public presaleStarted;

    // timestamp for when presale would end
    uint256 public presaleEnded;

    modifier onlyWhenNotPaused {
        require(!_paused, "Contract currently paused");
        _;
    }

    /**
      * @dev ERC721 constructor takes in a `name` and a `symbol` to the token 
      * collection.
      * name in our case is `Crypto Devs` and symbol is `CD`.
<strong>      * Constructor for Crypto Devs takes in the baseURI to set _baseTokenURI for the 
</strong><strong>      * collection.
</strong>      * It also initializes an instance of whitelist interface.
      */
    constructor (string memory baseURI, address whitelistContract) ERC721("Crypto Devs", "CD") {
        _baseTokenURI = baseURI;
        whitelist = IWhitelist(whitelistContract);
    }

    /**
    * @dev startPresale starts a presale for the whitelisted addresses
      */
    function startPresale() public onlyOwner {
        presaleStarted = true;
        // Set presaleEnded time as current timestamp + 5 minutes
        // Solidity has cool syntax for timestamps (seconds, minutes, hours, days, 
        // years)
        presaleEnded = block.timestamp + 5 minutes;
    }

    /**
      * @dev presaleMint allows a user to mint one NFT per transaction during the 
      * presale.
      */
    function presaleMint() public payable onlyWhenNotPaused {
        require(presaleStarted &#x26;&#x26; block.timestamp &#x3C; presaleEnded, "Presale is not running");
        require(whitelist.whitelistedAddresses(msg.sender), "You are not whitelisted");
        require(tokenIds &#x3C; maxTokenIds, "Exceeded maximum Crypto Devs supply");
        require(msg.value >= _price, "Ether sent is not correct");
        tokenIds += 1;
        //_safeMint is a safer version of the _mint function as it ensures that
        // if the address being minted to is a contract, then it knows how to deal 
        // with ERC721 tokens
        // If the address being minted to is not a contract, it works the same way as 
        // _mint
        _safeMint(msg.sender, tokenIds);
    }

    /**
    * @dev mint allows a user to mint 1 NFT per transaction after the presale has 
    * ended.
    */
    function mint() public payable onlyWhenNotPaused {
        require(presaleStarted &#x26;&#x26; block.timestamp >=  presaleEnded, "Presale has not ended yet");
        require(tokenIds &#x3C; maxTokenIds, "Exceed maximum Crypto Devs supply");
        require(msg.value >= _price, "Ether sent is not correct");
        tokenIds += 1;
        _safeMint(msg.sender, tokenIds);
    }

    /**
    * @dev _baseURI overides the Openzeppelin's ERC721 implementation which by 
    * default
    * returned an empty string for the baseURI
    */
    function _baseURI() internal view virtual override returns (string memory) {
        return _baseTokenURI;
    }

    /**
    * @dev setPaused makes the contract paused or unpaused
      */
    function setPaused(bool val) public onlyOwner {
        _paused = val;
    }

    /**
    * @dev withdraw sends all the ether in the contract
    * to the owner of the contract
      */
    function withdraw() public onlyOwner  {
        address _owner = owner();
        uint256 amount = address(this).balance;
        (bool sent, ) =  _owner.call{value: amount}("");
        require(sent, "Failed to send Ether");
    }

      // Function to receive Ether. msg.data must be empty
    receive() external payable {}

    // Fallback function is called when msg.data is not empty
    fallback() external payable {}
}
</code></pre>

Install **`dotenv` ** package to be able to import the env file and use it in our config.&#x20;

Inside **`hardhat-tutorial`** directory:

```shell
npm install dotenv
```

