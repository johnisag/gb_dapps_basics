---
description: Launch your own Initial Coin Offering
---

# Launch ICO

### Requirements

* There should be a max of `10,000 CD` tokens.
* Every `Crypto Dev` NFT holder should get 10 tokens for free but they would have to pay the gas fees.
* The price of one CD at the time of ICO should be `0.001 ether`.
* There should be a website that users can visit for the ICO.

### Basics

What is an ERC20?

* ERC-20 is a technical standard; it is used for all smart contracts on the Ethereum blockchain for token implementation and provides a list of rules that all Ethereum-based tokens must follow.
* Please look at all the ERC20 [functions](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20) before moving ahead.

### Smart Contract

**Setup a Hardhat project**

```shell
mkdir ico
cd ico
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

Create **ICryptoDevs.sol** inside **./contracts**

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface ICryptoDevs {
    /**
     * @dev Returns a token ID owned by `owner` at a given `index` of its token list.
     * Use along with {balanceOf} to enumerate all of ``owner``'s tokens.
     */
    function tokenOfOwnerByIndex(address owner, uint256 index)
        external
        view
        returns (uint256 tokenId);

    /**
     * @dev Returns the number of tokens in ``owner``'s account.
     */
    function balanceOf(address owner) external view returns (uint256 balance);
}
```

Create **CryptoDevToken.sol** inside **./contracts**

{% code overflow="wrap" %}
```solidity
// SPDX-License-Identifier: MIT
  pragma solidity ^0.8.0;

  import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
  import "@openzeppelin/contracts/access/Ownable.sol";
  import "./ICryptoDevs.sol";

  contract CryptoDevToken is ERC20, Ownable {
      // Price of one Crypto Dev token
      uint256 public constant tokenPrice = 0.001 ether;
      // Each NFT would give the user 10 tokens
      // It needs to be represented as 10 * (10 ** 18) as ERC20 tokens are represented by the smallest denomination possible for the token
      // By default, ERC20 tokens have the smallest denomination of 10^(-18). This means, having a balance of (1)
      // is actually equal to (10 ^ -18) tokens.
      // Owning 1 full token is equivalent to owning (10^18) tokens when you account for the decimal places.
      // More information on this can be found in the Freshman Track Cryptocurrency tutorial.
      uint256 public constant tokensPerNFT = 10 * 10**18;
      // the max total supply is 10000 for Crypto Dev Tokens
      uint256 public constant maxTotalSupply = 10000 * 10**18;
      // CryptoDevsNFT contract instance
      ICryptoDevs CryptoDevsNFT;
      // Mapping to keep track of which tokenIds have been claimed
      mapping(uint256 => bool) public tokenIdsClaimed;

      constructor(address _cryptoDevsContract) ERC20("Crypto Dev Token", "CD") {
          CryptoDevsNFT = ICryptoDevs(_cryptoDevsContract);
      }

      /**
       * @dev Mints `amount` number of CryptoDevTokens
       * Requirements:
       * - `msg.value` should be equal or greater than the tokenPrice * amount
       */
      function mint(uint256 amount) public payable {
          // the value of ether that should be equal or greater than tokenPrice * 
          // amount;
          uint256 _requiredAmount = tokenPrice * amount;
          require(msg.value >= _requiredAmount, "Ether sent is incorrect");
          // total tokens + amount <= 10000, otherwise revert the transaction
          uint256 amountWithDecimals = amount * 10**18;
          require(
              (totalSupply() + amountWithDecimals) <= maxTotalSupply,
              "Exceeds the max total supply available."
          );
          // call the internal function from Openzeppelin's ERC20 contract
          _mint(msg.sender, amountWithDecimals);
      }

      /**
       * @dev Mints tokens based on the number of NFT's held by the sender
       * Requirements:
       * balance of Crypto Dev NFT's owned by the sender should be greater than 0
       * Tokens should have not been claimed for all the NFTs owned by the sender
       */
      function claim() public {
          address sender = msg.sender;
          // Get the number of CryptoDev NFT's held by a given sender address
          uint256 balance = CryptoDevsNFT.balanceOf(sender);
          // If the balance is zero, revert the transaction
          require(balance > 0, "You dont own any Crypto Dev NFT's");
          // amount keeps track of number of unclaimed tokenIds
          uint256 amount = 0;
          // loop over the balance and get the token ID owned by `sender` at a given `index` of its token list.
          for (uint256 i = 0; i < balance; i++) {
              uint256 tokenId = CryptoDevsNFT.tokenOfOwnerByIndex(sender, i);
              // if the tokenId has not been claimed, increase the amount
              if (!tokenIdsClaimed[tokenId]) {
                  amount += 1;
                  tokenIdsClaimed[tokenId] = true;
              }
          }
          // If all the token Ids have been claimed, revert the transaction;
          require(amount > 0, "You have already claimed all the tokens");
          // call the internal function from Openzeppelin's ERC20 contract
          // Mint (amount * 10) tokens for each NFT
          _mint(msg.sender, amount * tokensPerNFT);
      }

      /**
        * @dev withdraws all ETH sent to this contract
        * Requirements:
        * wallet connected must be owner's address
        */
      function withdraw() public onlyOwner {
        uint256 amount = address(this).balance;
        require(amount > 0, "Nothing to withdraw; contract balance empty");
        
        address _owner = owner();
        (bool sent, ) = _owner.call{value: amount}("");
        require(sent, "Failed to send Ether");
      }

      // Function to receive Ether. msg.data must be empty
      receive() external payable {}

      // Fallback function is called when msg.data is not empty
      fallback() external payable {}
  }
```
{% endcode %}

Go to [Quicknode](https://www.quicknode.com/?utm\_source=learnweb3\&utm\_campaign=generic\&utm\_content=sign-up\&utm\_medium=learnweb3) and sign up for an account.&#x20;

Install **`dotenv` ** package to be able to import the env file and use it in our config.&#x20;

Inside **`hardhat-tutorial`** directory:

```shell
npm install dotenv
```

Create a `.env` file in the `hardha_tutorial` folder and add the following lines

```python
QUICKNODE_HTTP_URL="add-quicknode-http-provider-url-here"
PRIVATE_KEY="add-the-private-key-here"
```

Create/replace ./**`scripts`**/**`deploy.js`**.

{% code overflow="wrap" %}
```javascript
const { ethers } = require("hardhat");
require("dotenv").config({ path: ".env" });
const { CRYPTO_DEVS_NFT_CONTRACT_ADDRESS } = require("../constants");

async function main() {
  // Address of the Crypto Devs NFT contract that you deployed in the previous module
  const cryptoDevsNFTContract = CRYPTO_DEVS_NFT_CONTRACT_ADDRESS;

  /*
    A ContractFactory in ethers.js is an abstraction used to deploy new smart 
    contracts,
    so cryptoDevsTokenContract here is a factory for instances of our CryptoDevToken 
    contract.
    */
  const cryptoDevsTokenContract = await ethers.getContractFactory(
    "CryptoDevToken"
  );

  // deploy the contract
  const deployedCryptoDevsTokenContract = await cryptoDevsTokenContract.deploy(
    cryptoDevsNFTContract
  );

  await deployedCryptoDevsTokenContract.deployed();
  // print the address of the deployed contract
  console.log(
    "Crypto Devs Token Contract Address:",
    deployedCryptoDevsTokenContract.address
  );
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```
{% endcode %}

**deploy.js** requires some constants.&#x20;

Create a **new folder** named **constants** inside the **hardhat-tutorial folder**.&#x20;

Create an **index.js** file inside the **constants** folder

```javascript
// Address of the NFT Contract that you deployed
const CRYPTO_DEVS_NFT_CONTRACT_ADDRESS = "address-of-the-nft-contract";
module.exports = { CRYPTO_DEVS_NFT_CONTRACT_ADDRESS };
```

Update **hardhat.config.js** file to use the **goerli** network

```javascript
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const QUICKNODE_HTTP_URL = process.env.QUICKNODE_HTTP_URL;
const PRIVATE_KEY = process.env.PRIVATE_KEY;

module.exports = {
  solidity: "0.8.4",
  networks: {
    goerli: {
      url: QUICKNODE_HTTP_URL,
      accounts: [PRIVATE_KEY],
    },
  },
};
```

**Compile and deploy**

```shell
# from within hardhat folder
npx hardhat compile
npx hardhat run scripts/deploy.js --network goerli
```

### dApp&#x20;

### dApp Deploy



