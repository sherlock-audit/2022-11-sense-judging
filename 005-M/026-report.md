8olidity

medium

# solmate does not check whether the token exists

## Summary
solmate does not check whether the token exists
## Vulnerability Detail

solmate won't check if the token is a contract or not.
A hacker could set traps for non existing tokens to steal future funds from users.

The `safeTransfer()` functions used in the contract are wrappers around the `solmate` library. [Solmate](https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9) will not check for contract existance.

```solidity
File: src/libraries/TransferHelper.sol
function safeTransferFrom(
    ERC20 token,
    address from,
    address to,
    uint256 amount
) internal {
    (bool success, bytes memory data) = address(token).call(
        abi.encodeWithSelector(ERC20.transferFrom.selector, from, to, amount)
    );

    require(success && (data.length == 0 || abi.decode(data, (bool))), "TRANSFER_FROM_FAILED");
}

function safeTransfer(
    ERC20 token,
    address to,
    uint256 amount
) internal {
    (bool success, bytes memory data) = address(token).call(
        abi.encodeWithSelector(ERC20.transfer.selector, to, amount)
    );

    require(success && (data.length == 0 || abi.decode(data, (bool))), "TRANSFER_FAILED");
}
```


## Impact
solmate does not check whether the token exists
## Code Snippet
https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/AutoRoller.sol#L177
```solidity
    function onSponsorWindowOpened(ERC20 stake, uint256 stakeSize) external {
        if (msg.sender != address(adapter)) revert OnlyAdapter();

        stake.safeTransferFrom(lastRoller, address(this), stakeSize);
```
## Tool used

Manual Review

## Recommendation

Looking at OpenZeppelin's implementation, a check is made on the code for the token address.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L36-L42
```solidity
address(token).code.length > 0
```