pashov

medium

# Solmate's ERC4626 implementation does not work correctly with fee-on-transfer `asset` tokens

## Summary
If a fee-on-transfer token is used as the `asset` in a vault then the vault shares math won't be correct

## Vulnerability Detail
Some tokens take a transfer fee (e.g. STA, PAXG), some do not currently charge a fee but may do so in the future (e.g. USDT, USDC). Also this fee can be variable and changed. If this is the case, when a user tries to withdraw his assets put in the vault, he might not get the correct amount, since when he deposited the fee could have been different than when others deposited.

## Impact
The impact can be value loss for a user if fee-on-transfer token is used as the `asset` of a vault. Since it is a special case it should be Medium severity
## Code Snippet
https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/AutoRoller.sol#L56

## Tool used

Manual Review

## Recommendation
On `deposit` and `mint` always check the contract's balance before and after the transfer and count only the difference.
