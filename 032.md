ctf_sec

medium

# A malicious early user/attacker can manipulate the share to take an unfair share of future users' deposits when maturity is not set.

## Summary

A well known attack vector for almost all shares based liquidity pool contracts, where an early user can manipulate the price per share and profit from late users' deposits because of the precision loss caused by the rather large value of price per share.

## Vulnerability Detail

A malicious early user can deposit() with 1 wei of asset token as the first depositor of the AutoRoller token, and get 1 wei of shares.

Then the attacker can send 10000e18 - 1 of asset tokens and inflate the price per share from 1.0000 to an extreme value of 1.0000e22 ( from (1 + 10000e18 - 1) / 1) .

As a result, the future user who deposits 19999e18 will only receive 1 wei (from 19999e18 * 1 / 10000e18) of shares token.

They will immediately lose 9999e18 or half of their deposits if they redeem() right after the deposit().

## Impact

The attacker can profit from future users' deposits. While the late users will lose part of their funds to the attacker.

## Code Snippet

https://github.com/transmissions11/solmate/blob/ed67feda67b24fdeff8ad1032360f0ee6047ba0a/src/mixins/ERC4626.sol#L46-L59

https://github.com/transmissions11/solmate/blob/ed67feda67b24fdeff8ad1032360f0ee6047ba0a/src/mixins/ERC4626.sol#L135-L139

https://github.com/transmissions11/solmate/blob/ed67feda67b24fdeff8ad1032360f0ee6047ba0a/src/mixins/ERC4626.sol#L129-L135

https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/AutoRoller.sol#L420-L423

## Tool used

Manual Review

## Recommendation

Consider requiring a minimal amount of share tokens to be minted for the first minter, and send a port of the initial mints as a reserve to the DAO so that the pricePerShare can be more resistant to manipulation.
