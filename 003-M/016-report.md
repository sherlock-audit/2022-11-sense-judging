koxuan

medium

# creating an AutoRoller will fail when using an adapter that uses a erc20 token with more than 18 decimals as target

## Summary
An arithmetic calculation in the `construtor()` function in `AutoRoller.sol` will cause an underflow and revert when creating a roller with an adapter that uses a erc20 token with more than 18 decimals as target.

## Vulnerability Detail
 `uint256 scalingFactor = 10**(18 - decimals)` underflows and reverts if `decimals` is more than 18
 

## Impact
User will be unable to create AutoRoller with adapter that uses a erc20 token with more than 18 decimals as target.

## Code Snippet
[AutoRoller.sol#L138](https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/AutoRoller.sol#L138)
```solidity

        uint256 scalingFactor = 10**(18 - decimals);

```

## Tool used
Manual Review

## Recommendation
`if else` clause can mitigate this by handling erc20 tokens with more than 18 decimals
```solidity
if (decimals > 18){
    // logic for handling more than 18 decimal points 
    // ie: scalingFactor = 1
} 
else{
    scalingFactor = 10**(18 - decimals);
}
```
