pashov

medium

# Some tokens (`USDT`) expect to always have zero allowances before approving a non-uint256.max amount

## Summary
The code approves non-zero and non-uint256.max amount for a token without first approving `0`. This will always revert if the token is `USDT`

## Vulnerability Detail
Some tokens (e.g. USDT, KNC) do not allow approving an amount M > 0 when an existing amount N > 0 is already approved. This is to protect from an ERC20 attack vector described [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit#heading=h.b32yfk54vyg9).
In `onSponsorWindowOpened` method for example we have `stake.safeApprove(address(periphery), stakeSize);` which will always revert if the `periphery` address still has allowance > 0 and `stake` token is `USDT`. The problem exists in `RollerPeriphery::approve` as well.

## Impact
The impact is DoS for using a common token - `USDT`. Still, it is a special case, so Medium severity should be appropriate
## Code Snippet
https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/AutoRoller.sol#L180
https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/RollerPeriphery.sol#L101
## Tool used

Manual Review

## Recommendation
Before doing `safeApprove`, always approve `0` amount first, like `token.safeApprove(to, 0)`