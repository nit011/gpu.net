# Swap 

This project is meant to be a permissionless way for users to swap assets between each other at a fair price.


## Swap Pools
The protocol starts as simply a `PoolFactory` contract. This contract is used to create new "pools" of tokens. It helps make sure every pool token uses the correct logic. But all the magic is in each `SwapPool` contract. 


For example:
1. User A has 10 USDC
2. They want to use it to buy DAI
3. They `swap` their 10 USDC -> WETH in the USDC/WETH pool
4. Then they `swap` their WETH -> DAI in the DAI/WETH pool

Every pool is a pair of `TOKEN X` & `WETH`. 

There are 2 functions users can call to swap tokens in the pool. 
- `swapExactInput`
- `swapExactOutput`

We will talk about what those do in a little. 

## Liquidity Providers
In order for the system to work, users have to provide liquidity, aka, "add tokens into the pool". 

### Why would I want to add tokens to the pool? 
The Swap protocol accrues fees from users who make swaps. Every swap has a `0.3` fee, represented in `getInputAmountBasedOnOutput` and `getOutputAmountBasedOnInput`. Each applies a `997` out of `1000` multiplier. That fee stays in the protocol. 

When you deposit tokens into the protocol,  you are rewarded with an LP token. You'll notice `SwapPool` inherits the `ERC20` contract. This is because the `SwapPool` gives out an ERC20 when Liquidity Providers (LP)s deposit tokens. This represents their share of the pool, how much they put in. When users swap funds, 0.03% of the swap stays in the pool, netting LPs a small profit. 

### LP Example
1. LP A adds 1,000 WETH & 1,000 USDC to the USDC/WETH pool
   1. They gain 1,000 LP tokens
2. LP B adds 500 WETH & 500 USDC to the USDC/WETH pool 
   1. They gain 500 LP tokens
3. There are now 1,500 WETH & 1,500 USDC in the pool
4. User A swaps 100 USDC -> 100 WETH. 
   1. The pool takes 0.3%, aka 0.3 USDC.
   2. The pool balance is now 1,400.3 WETH & 1,600 USDC
   3. aka: They send the pool 100 USDC, and the pool sends them 99.7 WETH



## Core Invariant 

`x * y = k`
- x = Token Balance X
- y = Token Balance Y
- k = The constant ratio between X & Y


Our protocol should always follow this invariant in order to keep swapping correctly!

## Make a swap

After a pool has liquidity, there are 2 functions users can call to swap tokens in the pool. 
- `swapExactInput`
- `swapExactOutput`

A user can either choose exactly how much to input (ie: I want to use 10 USDC to get however much WETH the market says it is), or they can choose exactly how much they want to get out (ie: I want to get 10 WETH from however much USDC the market says it is.)

