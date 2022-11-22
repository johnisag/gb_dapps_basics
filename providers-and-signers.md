# Providers and Signers

Communicate with an Ethereum node:

* A node contains the blockchain state
  * Allow us to read data (no broadcast)
* Broadcast transactions to miners
  * &#x20;Write data to the blockchain

**A `Provider`** is an Ethereum node connection that allows you to read data from its state

* calling read-only functions in smart contracts
* fetching balances of accounts
* fetching transaction details
* **can only for reading data**

**A `Signer`** is an Ethereum node connection that allows you to write data to the blockchain

* calling write functions in smart contracts
* transferring ETH between accounts
* **can do anything that a Provider can do, **<mark style="color:orange;">**both reading and writing data**</mark>

**The `Signer`** needs **access** to **a `Private Key`** so it can use for making transactions on behalf of an account

**Wallets** like **Metamask**, by default, <mark style="color:orange;">**inject a**</mark><mark style="color:orange;">** **</mark><mark style="color:orange;">**`Provider`**</mark><mark style="color:orange;">** **</mark><mark style="color:orange;">**into your browser.**</mark>

* Metamask also allows websites <mark style="color:purple;">to request a Signer</mark>
* When a dApp attempts to send a transaction to the blockchain, the Metamask window pops up asking the user to confirm the action.
