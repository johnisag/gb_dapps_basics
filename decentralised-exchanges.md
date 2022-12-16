---
description: Diving into Decentralized Exchanges (Uniswap V1)
---

# Decentralised Exchanges

### Birth of DEXs

The idea of a decentralized exchange is simple - allow users to trade their crypto directly on-chain through smart contracts without giving up control of their private keys.

The birth of modern decentralized exchanges was primarily led by [Uniswap](https://uniswap.org/). Not only is Uniswap the leading decentralized exchange on Ethereum, it is THE leading dApp on Ethereum in general.

### The complication

Centralized exchanges typically work on an order-book system.

Order-book based exchanges were attempted on Ethereum, with the most significant example being [0xProject](https://0x.org/) but due to the high gas required for all the storage and matching algorithms, it was challenging to attract users.

### Uniswap V1, V2, V3

**V1**

* Allows only swaps between ether and a token
* Chained swaps were also possible to allow token-token swaps
* Chained swaps would allow for a `TokenA <> TokenB` swap by first swapping one of them for ETH, and then swapping the ETH for the second token.

**V2**

* Huge improvement of V1
* Allows direct swaps between any ERC20 tokens
* Allows chained swaps between any pairs

**V3:**

* Allows liquidity providers to remove a bigger portion of their liquidity from pools and still keep getting the same rewards

### **Market Makers**

<mark style="color:orange;">**Uniswap**</mark> is an **Automated Market Maker**

**Market Makers** are entities that **provide liquidity (assets)** to trading markets

In non-orderbook systems, liquidity is what allows trading to be possible. Example:

* If you want to sell BTC to buy ETH, the exchange must have an ETH balance you can purchase from in exchange for BTC
* Some trading pairs have very high liquidity (eg. BTC/ETH trading pair), but some have extremely low or no liquidity at all (eg. scam tokens, or newly created tokens).

A **DEX must have enough liquidity** to function and serve as an alternative to centralized exchanges.

<mark style="color:orange;">**Uniswap**</mark>** allows anyone to be a market maker**

* This is what makes Uniswap an **automated market maker**
* **Any user can deposit funds to a specific trading pair and add liquidity**, and in exchange **earn** money for doing so **through trading fees taken from the users**.

### Functional Requirements

At the core of Uniswap is one math function:&#x20;

* **x \* y = k**

Assume we have a trading pair for `ETH / LW3 Token`

**x** = reserve balance of `ETH` in the trading pool

**y** = reserve balance of `LW3 Token` in the trading pool

**k** = a constant

This formula is responsible for calculating prices, deciding how much `LW3 Token` would be received in exchange for a certain amount of `ETH`, or vice versa.

**NOTE: It doesn't matter if we use `x` to represent the reserve of `ETH` or `LW3 Token` as long as `y` represents the opposite.**

Let's try to write that as a formula:

**`(x + Δx) * (y - Δy) = k`**

**`Δx`** is the amount being provided by the user for sale&#x20;

**`Δy`** is the amount the user is receiving from the DEX in exchange for `Δx`.

Since **`k`** is a constant, we can compare the above two formulas to get:

* **Δy = (y \* Δx) / (x + Δx)**

**In solidity:**

{% code overflow="wrap" %}
```solidity
function calculateOutputAmount(
    uint inputAmount, 
    uint inputReserve, 
    uint outputReserve) private pure returns (uint) {    
    uint outputAmount = (outputReserve * inputAmount) / (inputReserve + inputAmount);
    return outputAmount;
}
```
{% endcode %}

**Example:**

Assume we have `100 ETH` and `200 LW3 Token` in the contract.

**What would happen if I want to swap 1 `ETH` for `LW3 Tokens`? Let's do the math.**

`inputAmount` = 1 ETH `inputReserve` = 100 ETH `outputReserve` = 200 LW3 Tokens

\=> `outputAmount` = `1.98019802` `LW3 Tokens`

**What would happen if instead I wanted to swap 2 `LW3 Tokens` for ETH?**

`inputAmount` = 2 LW3 Tokens `inputReserve` = 200 LW3 Tokens `outputReserve` = 100 ETH

\=> `outputAmount` = `0.999` `ETH`

These amounts are very close to the `1:2` ratio of tokens present in the contract reserves, but slightly smaller. Why?

The product formula we use for price calculations is actually a hyperbola.

![](.gitbook/assets/image.png)

### Slippage

Since we don't get tokens in the exact ratio of reserves, this leads to an interesting implication of the math. The price function causes **slippage** in the price.

The bigger the amount of tokens being traded relative to their reserve values, the lower the price would be.

**Example:**

Let's say I wanted to try to drain out the entire pool, and sell `200 ETH`.

`inputAmount` = 200 ETH `inputReserve` = 100 ETH `outputReserve` = 200 LW3 Tokens

\=> `outputAmount` = `133.333` LW3 Tokens

\=> As you can see, when we're trying to drain out the pool, we're only getting close to a half of what we expect.

Some may see this as a flaw of automated market makers that follow `x*y = k`, but it's actually not. It is the same mechanism that protects pools from being completely drained.&#x20;

**This also aligns with the law of supply and demand: the higher the demand relative to the supply, the more costly it is to buy that supply.**

### Who sets the initial price?

When a new cryptocurrency is created, there is no liquidity for trading pairs involving that token.

Therefore, the first person adding liquidity to the pool gets to set a price.

<mark style="color:orange;">**Adding liquidity involves adding tokens from both sides of the trading pair - you cannot add liquidity for just one side**</mark><mark style="color:orange;">.</mark>

When the first person adds liquidity, it creates a reserve balance and sets the initial `x` and `y` values

From that point onward, we can do price calculations when swapping between tokens.

A simple implementation of the **`addLiquidity` ** in **Solidity**:

```solidity
// This function accepts ETH and a token from the user.
function addLiquidity(uint256 tokenAmount) public payable {
    IERC20 token = IERC20(tokenAddress);
    token.transferFrom(msg.sender, address(this), tokenAmount);
}
```

**This implementation is incomplete!**

* A second person can come along, and add liquidity in a completely different ratio of reserves which would massively affect price calculations.
* We do not want to allow for such price manipulations, and we want prices on the decentralized exchange to be as close to those on centralized exchanges as possible.

We must ensure that **anyone adding additional liquidity** to the pool is doing so **in the same proportion** as that already established in the pool.&#x20;

* We only want to allow arbitrary ratios when the pool is completely empty.

A updated implementation of the **`addLiquidity` ** in **Solidity**:

```solidity
function addLiquidity(uint tokenAmount) public payable {
    // assuming a hypothetical function
    // that returns the balance of the
    // token in the contract
    if (getReserve() == 0) {
        IERC20 token = IERC20(tokenAddress);
        token.transferFrom(msg.sender, address(this), tokenAmount);
    } else {
        // msg.value is the amount sending with the func call - ETH 101
        uint ethReserve = address(this).balance - msg.value;
        uint tokenReserve = getReserve();
        uint proportionalTokenAmount = (msg.value * tokenReserve) / ethReserve;
        require(tokenAmount >= proportionalTokenAmount, "incorrect ratio of tokens provided");

        IERC20 token = IERC20(tokenAddress);
        token.transferFrom(msg.sender, address(this), proportionalTokenAmount);
    }
}
```

### **LP Tokens**

But what if a liquidity provider wants to withdraw their liquidity from the pool?

* We need a way to reward the liquidity providers for their tokens
* Nobody would put tokens in a third-party contract if they are not getting something out of it

The only good solution for this is to collect a small fee on each token swap and distribute the fees amongst the liquidity providers, based on how much liquidity they provided.

There is a quite elegant solution to do this: **Liquidity Provider Tokens (LP Tokens)**

**LP Tokens work as shares.**

1. You get LP-tokens in exchange for your liquidity
2. Amount of tokens you get is proportional to your share of the liquidity in the pool
3. Fees are distributed proportional to how many LP-tokens you own
4. LP-tokens can be exchanged back for the liquidity + earned fees

**Additional Requirements:**

1. Issued shares must always be correct. When someone else deposits or removes liquidity after you, your shares should remain and maintain correct values.
2. Writing data to the chain can be expensive (gas fees) - we want to reduce the maintainence costs of LP-tokens as much as possible.

Imagine we issue a lot of tokens - say a few billion. The first time someone adds liquidity, they own 100% of the liquidity in the pool. So do we give them all few billion tokens?

This leads to the problem that when a second person adds liquidity, the shares need to be recalculated which is expensive due to gas fees.

Alternatively, if we choose to distribute only a portion of the tokens initially, we risk hitting the limit, which will also eventually force us to recalculate existing shares.

The only good solution seems to have no supply limit at all, and mint new tokens whenever new liquidity is added. This allows for infinite growth, and if we do the math carefully, we can make sure issued shares remain correct whenever liquidity is added or removed.

**So, how do we calculate the amount of LP-tokens to be minted when liquidity is added?**

**Uniswap V1** calculates the amount **proportionate to the ETH reserve**. The following equation shows how the amount of new LP-tokens is calculated depending on how much ETH is deposited:

**`amountMinted = totalAmount * (ethDeposited / ethReserve)`**

We can update `addLiquidity` function to mint LP-tokens when liquidity is added:

```solidity
function addLiquidity(uint tokenAmount) public payable {
    if (getReserve() == 0) {
        ...

        uint liquidity = address(this).balance;
        _mint(msg.sender, liquidity);
    } else {
        ...
        uint liquidity = (totalSupply() * msg.value) / ethReserve;
        _mint(msg.sender, liquidity);
    }
}
```

Now we have LP-tokens, we can also use them to calculate how much underlying tokens to return when someone wants to withdraw their liquidity in exchange for their LP-tokens.

We don't need to remember how much they originally deposited. Since LP-tokens are proportional to amount of ETH deposited, we can rearrange the above formula to calculate the amount of ETH to return, and proportionately calculate the amount of tokens to return.

### Fees

Now to collect fees on swaps and distribute them amongst liquidity providers, we need to think about a couple of things:

* Do we collect fees in ETH or tokens?
* Do we pay rewards in ETH or tokens?
* How do we collect the fees from each swap?
* How to distribute the fees amongst all liquidity providers?

These may seem difficult questions to answer, but we actually have everything we need to answer them.

1. Traders are already sending ether/tokens to the contract. Instead of asking for an explicit fee, we can just deduct some amount from the ether/tokens they are sending.
2. We can just add the fees to the reserve balance. This means, over time, the reserves will grow!
3. We can collect fees in the currency of the asset being deposited by the trader. Liquidity providers thus get a balanced amount of ether and tokens proportional to their share of LP-tokens.

**Uniswap takes a 0.03% fees from each swap**.&#x20;

Let's say we take 1% to keep things simple. Adding fees to the contract is as simple as making a few edits to our price calculation formula:

We had&#x20;

**`outputAmount = (outputReserve * inputAmount) / (inputReserve + inputAmount)`**

Now,

**`outputAmountWithFees = 0.99 * outputAmount`**

But, Solidity does not support floating point operations. So for Solidity we rewrite the formula as such:

**`outputAmountWithFees = (outputAmount * 99) / 100`**
