0xSmartContract

medium

# Vulnerability related to ‘Optimizer Bug Regarding Memory Side Effects of Inline Assembly’

## Summary
Solidity versions 0.8.13 and 0.8.14 are vulnerable to a recently reported [optimizer bug](https://blog.soliditylang.org/2022/06/15/inline-assembly-memory-side-effects-bug/) related to inline assembly. Solidity 0.8.15 has been released with a fix.

## Vulnerability Detail
AutoRoller.sol inherits solidity contracts from  solmate FixedPointMathLib and ERC4626 , and both these uses inline assembly, and optimization is enabled while compiling.


## Impact
This bug only occurs under very specific conditions: the legacy optimizer must be enabled rather than the IR pipeline (true for the current project configuration), and the affected assembly blocks must not refer to any local Solidity variables.

## Code Snippet

[AutoRoller.sol#L5](https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/AutoRoller.sol#L5)

[AutoRoller.sol#L7](https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/AutoRoller.sol#L7)

[FixedPointMathLib.sol#L36-L51](https://github.com/transmissions11/solmate/blob/main/src/utils/FixedPointMathLib.sol#L36-L51)


```solidity
contracts/src/AutoRoller.sol:
 
  4: import { ERC20 } from "solmate/tokens/ERC20.sol";
  5: import { FixedPointMathLib } from "solmate/utils/FixedPointMathLib.sol";
  6: import { SafeTransferLib } from "solmate/utils/SafeTransferLib.sol";
  7: import { ERC4626 } from "solmate/mixins/ERC4626.sol";

contracts/foundry.toml:
  1: [profile.default]
  8: optimizer_runs = 10000
```
## Tool used

Manual Review

## Recommendation

Use recent Solidity version 0.8.15 which has the fix for these issues.