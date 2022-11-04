YieldProtocol

unlabeled

# DoS by sponsoring the next series

## Summary

An attacker can sponsor a series before the AutoRoller, which then can't `roll` anymore.

## Vulnerability Detail

The AutoRoller, on `onSponsorWindowOpened`, calls `periphery.sponsorSeries(address(adapter), maturity)`. The `adapter` is immutable and the `maturity` is determined from the `targetDuration` just before the call to `periphery`.

`periphery.sponsorSeries` will call `spaceFactory.create(adapter, maturity)` to create the Space pool.

`spaceFactory.create(address adapter, uint256 maturity)` will revert if the pool has already been created. Pools can’t be removed from SpaceFactory once created.

`Periphery.sponsorSeries` is permissionless. Anyone can create a new series and pool with it.

An attacker only needs to find out what the next maturity is for a given AutoRoller, and then create a series for the same adapter and maturity using `Periphery.sponsorSeries`. From then on, any `roll` from the AutoRoller will revert [here](https://github.com/sense-finance/space-v1/blob/15449823c420f41387573283bb3c5ddff30e4021/src/SpaceFactory.sol#L78).

## Impact

Users can still remove their liquidity, but to unblock the AutoRoller the SpaceFactory would have to be replaced, which might bring other problems.

## Tool used

Manual Review

## Recommendation

Can't think of a good solution here, since the protocol aims to be permissionless.
