# ERC20 Approval Flow

Accepting payments in ERC20 tokens is not as simple as just making a function **`payable`** in Solidity.

* **`Payable`** It is only good for ETH payments
* If you want to use your own ERC20 cryptocurrency, the flow to do that is a little more complicated.

The **`ERC20`** standard comes with the concept of **Allowance**.&#x20;

An example:

* Alice wants to sell her NFT
* Alice wants to accept payment for her NFT in her own cryptocurrency, AliceCoin.
* Alice's NFT costs 10 AliceCoin
* Bob owns AliceCoin
* Bob wants to buy Alice's NFT
* Bob needs a way to call a function on Alice's NFT smart contract, which would take the 10 Alicecoin payment and send him his NFT.
* Since smart contracts cannot accept Alicecoin as payment directly, Alice has coded up the **ERC20 Approval and Transfer** flow in her NFT contract

**Alicecoin** is an ERC20 token. ERC20 comes inbuilt with a few functions that relate to the Allowance concept.

<mark style="color:orange;">**approve(address spender, uint256 amount)**</mark>

* This allows a user to **approve** a different address to spend up to **amount** tokens on their behalf. i.e. This function provides an Allowance to the **spender** of up to **amount**

<mark style="color:orange;">**transferFrom(address from, address to, uint256 amount)**</mark>

* Allows a user to transfer the **amount** token **from** to **to**.
* If the user calling the function is the same as the **from** address, tokens are removed from the user's balance.
* If the user is someone other than the **from** address, the **from** address must have in the past, given the user allowance to spend the **amount** of tokens using the **approve** function.

Continuing with the example flow:

* Bob gives Alice's NFT contract the allowance to spend up to 10 of his **Alicecoin** using the <mark style="color:orange;">**approve()**</mark> <mark style="color:orange;"></mark><mark style="color:orange;"></mark> <mark style="color:orange;"></mark><mark style="color:orange;">**function**</mark>
* Bob calls the function to purchase Alice's NFT on her NFT contract
* The purchase function internally calls <mark style="color:orange;"></mark> <mark style="color:orange;"></mark><mark style="color:orange;">**transferFrom() function**</mark> <mark style="color:orange;"></mark><mark style="color:orange;"></mark> on **Alicecoin** and transfers 10 **Alicecoin** from Bob's account to Alice's account.
* Since the contract was given the allowance to spend up to 10 **Alicecoin** by Bob earlier, this action is permitted.
* Alice, therefore, receives her 10 Alicecoin, and Bob gets his NFT.

**Note** how Bob had to give approval to the contract, so the contract could _pull_ Bob's tokens from his account.

Therefore, Bob essentially had to do two transactions to replicate the behaviour of what could be done in one transaction if payment was being accepted in ETH.

* **Transaction 1** - Give allowance to the contract&#x20;
* **Transaction 2** - Call the contract function, which internally uses the allowance to transfer Bob's tokens to a different address

<mark style="color:red;">So, if you were building a dApp where you needed users to pay your smart contract using an ERC20 token, you would also need to make them do both transactions. Simply calling your contract function, without first having your users provide allowance to your contract, will cause the function call to fail.</mark>
