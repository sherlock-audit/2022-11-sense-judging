CRYP70

medium

# Adversary Can Front-Run Swap Transactions To Receive Funds For Free

## Summary
The `_swap()` function on line `730` of the `AutoRoller` contract  is used to implement a swap of tokens against the balancer vault. This function is called when executing a `roll()` and before a withdrawal when exiting LP shares. It takes in a `BalancerVault.SingleSwap` instance and uses this to call a swap. when `balancerVault.swap()` is called passing in the request, the issue occurs when the user is willing to accept a minimum of zero tokens in return. 

## Vulnerability Detail
Since `0` is supplied to `balancerVault.swap(request, funds, 0, type(uint256).max);` [here](https://github.com/sense-finance/auto-roller/blob/b3f33b70aafeed108c1505271bf1301df4ae2d45/src/AutoRoller.sol#L736) , an attacking user can front run the user initiating the swap and implement an exchange for zero assets out.  

A reference to the balancer vault's swap function can be seen [here](https://github.com/balancer-labs/balancer-v2-monorepo/blob/master/pkg/vault/contracts/Swaps.sol#L58-L107). On line 100, it can be seen that the swap limit is enforced as apart of the balancer vault but is made redundant when 0 is hardcoded when this function is called.

## Impact
The impact of this could be an unfair swap where the user initiating the swap is forced to accept a minimum of zero tokens back because of hard coding. This was rated as a medium in severity because the attacking user would have to be actively monitoring the mempool in order to execute the attack. 

## Code Snippet
https://github.com/sense-finance/auto-roller/blob/b3f33b70aafeed108c1505271bf1301df4ae2d45/src/AutoRoller.sol#L736

## Tool used
Manual Review

## Recommendation
I recommend allowing the user to choose how many tokens they are willing to accept back by passing a `uint256 limit` parameter into functions calling the `_swap()` function. 